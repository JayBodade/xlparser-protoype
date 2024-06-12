<template>
    <div style="width: 70%;margin: auto;">
      <h5>Extract Data From Templates</h5>
        <div class="d-flex">
    <select class="form-select mb-3 mx-2"  v-model="selectedModel" @change="onChange">
      <option v-for="(item, index) in fieldMeta" :key="index" :value="item">
       {{ item?.data?.name }}
      </option>
    </select>
    <button   type="button" class="btn btn-primary mb-3" @click="resetAll()"> Reset</button>
</div>
    <div v-if="selectedModel">
      <input ref="fileInput" type="file" class="form-control mb-3" @change="hanldeFileInput">
    </div>
    <div class="container" v-if="excel2DArray.length">
        <h5>Info</h5>
      <div v-for="(item, index) in selectedModel?.data?.template" :key="index">
        <div  class="mb-2 p-2 bg-light border rounded">
          {{ item.name }} - {{ item.val }}
        </div>
        <!-- <div v-else-if="item.type == 'Table'" class="table-responsive mb-3">
          <table class="table table-hover table-striped table-bordered">
            <thead class="thead-dark">
              <tr>
                <th v-for="(header, index) in item.headers" :key="index">{{ header }}</th>
              </tr>
            </thead>
            <tbody>
              <tr v-for="(tr, rowIndex) in item.table" :key="rowIndex">
                <td v-for="(td, colIndex) in tr" :key="colIndex">{{ td }}</td>
              </tr>
            </tbody>
          </table>
        </div> -->
        <!-- <div v-else-if="item.type == 'Summary'">
            <table class="table table-hover table-striped table-bordered">
                <thead class="thead-dark">
            <tr>
                <th v-for="(header, index) in item.total" :key="index">{{ header }}</th>
              </tr>
            </thead>
            </table>

        </div> -->
      </div>
      <h5 v-if="selectedModel && selectedModel.data && selectedModel.data.tableInfo && selectedModel.data.tableInfo.headers" >Table Info</h5>
      <div class="table-responsive mb-3">
          <table class="table table-hover table-striped table-bordered" v-if="selectedModel && selectedModel.data && selectedModel.data.tableInfo">
            <thead class="thead-dark" v-if="selectedModel && selectedModel.data && selectedModel.data.tableInfo && selectedModel.data.tableInfo.headers">
              <tr>
                <th v-for="(header, index) in selectedModel?.data?.tableInfo.headers" :key="index">{{ header }}</th>
              </tr>
            </thead>
            <tbody  v-if="selectedModel && selectedModel.data && selectedModel.data.tableInfo && selectedModel.data.tableInfo.tableData">
              <tr v-for="(tr, rowIndex) in selectedModel?.data?.tableInfo.tableData" :key="rowIndex">
                <td v-for="(td, colIndex) in tr" :key="colIndex">{{ td }}</td>
              </tr>
            </tbody>
          </table>
        </div>
        <div >
            <h5 v-if="selectedModel && selectedModel.data && selectedModel.data.summaryInfo && selectedModel.data.summaryInfo.summaryObj">Summary</h5>
            <table class="table table-hover table-striped table-bordered" v-if="selectedModel && selectedModel.data && selectedModel.data.summaryInfo && selectedModel.data.summaryInfo.summaryObj">
                <thead class="thead-dark">
            <tr v-for="(header, index) in  Object.keys(selectedModel?.data?.summaryInfo?.summaryObj)" :key="index">
             <td>{{ header }}</td> <td>{{ selectedModel?.data?.summaryInfo?.summaryObj[header] || '-' }}</td>
             
              </tr>
            </thead>
            </table>
    </div>
  </div>
  </div>
</template>

<script>
import readXlsxFile from 'read-excel-file'
import { db } from '@/firebase';
export default {
    data() {
        return {
            fieldMeta: [],
            selectedModel: null,
            isSelected: false,
            excel2DArray: [],
            clonedMeta : [],
        }
    },
    methods: {
        getFieldMeta() {
            console.log("running this");
            db.collection("template").get()
                .then((querySnapshot) => {
                    querySnapshot.forEach((doc) => {
                        console.log(doc.id, " => ", doc.data());
                        this.fieldMeta.push({ ID: doc.id, data: doc.data() });
                    });
                    console.log(this.fieldMeta);
                    this.clonedMeta = [...this.fieldMeta];

                })
                .catch((error) => {
                    console.log("Error getting documents: ", error);
                });
        },
        onChange() {
            console.log(this.selectedModel);
            this.isSelected = true;
        },
        checkAllValAreNull(arr){
            let allIsNull = true;
            arr.forEach(element => {
                if(element != null){
                    allIsNull = false
                }
            });

            return allIsNull;
        },
        findRow(text,col){

            for(let i=0;i<this.excel2DArray.length;i++){
               if(this.excel2DArray[i][col] == text) {
                return i;
               }
            }

            return -1;
            
        },
        hanldeFileInput(e) {
            const file = e.target.files[0];
            readXlsxFile(file).then((data) => {
                
                console.log("here");
                this.excel2DArray = data;
                this.selectedModel.data.template.forEach(element => {
                    console.log("in for loop");
                  if(element.row != '' && element.col != ''){
                    element.val = this.excel2DArray[parseInt(element.row) - 1][parseInt(element.col) - 1];
                  }
                  
                })
               let tableInfo = this.selectedModel.data.tableInfo;
               console.log("after for loop")
               if(tableInfo.tableRow != '' && tableInfo.col != ''){
               tableInfo.headers = this.excel2DArray[parseInt(tableInfo.tableRow) - 1];
               tableInfo.tableData = [];
               for(let i=parseInt(tableInfo.tableRow);;i++){
                if(i == this.excel2DArray.length) break;
                if(this.checkAllValAreNull(this.excel2DArray[i]))break;
                else tableInfo.tableData.push(this.excel2DArray[i]);
               }
               console.log("after 2d for loop");
               this.selectedModel.data.tableInfo = tableInfo;
            }
              
               let summary = this.selectedModel.data.summaryInfo;
             
               if(summary.summaryCol != ''){
                let rowIndex = this.findRow(summary.summaryCellText,parseInt(summary.summaryCol)-1);
              summary.summaryObj = {};
               if(rowIndex >= 0){
                  summary.total = this.excel2DArray[rowIndex]
                  tableInfo.headers.forEach((element,index )=> {
                        console.log(element);
                    summary.summaryObj[element] = summary.total[index] == summary.summaryCellText ? '-' : summary.total[index]  ;
                  });

               }
               
               this.selectedModel.data.summaryInfo = summary
               }
               




            })
        },
        resetAll(){
           this.selectedModel=null;
           this.fieldMeta=[...this.clonedMeta];
           this.excel2DArray = [];
        }
    },


    created() {
        this.getFieldMeta();
    }




}

</script>

<style></style>