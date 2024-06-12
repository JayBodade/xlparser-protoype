<template>
  <div style="width: 70%;margin: auto;">
    <h5>Add Template</h5>
    <input type="file" class="form-control mb-3" @change="handleFileChange">
    
    <div v-if="excel2DArray.length" class="mt-5" style="width: 100%; margin: auto;">
      <div class="p-4 mb-4 border border-secondary rounded">
        <input type="text" v-model="templateName"  class="form-control me-2 mb-2" placeholder="Enter Template Name">
        <h5>Info</h5>
        <div v-for="(field, index) in fieldMeta" :key="index" class="d-flex mb-3">
          <input type="text" v-model="field.name" class="form-control me-2" placeholder="Enter field Name">
          <!-- <select v-model="field.type" class="form-select me-2">
            <option value="Text">Text</option>
            <option value="Table">Table</option>  
            <option value="Summary">Summary</option>
          </select> -->
        
         
          <input type="number"  v-model="field.row" class="form-control me-2" placeholder="Enter row number">
          <input type="number" v-model="field.col" class="form-control me-2" placeholder="Enter columns number">
          <button class="btn btn-primary" @click="remove(index)">Remove</button>
        </div>
       
        <div class="d-flex justify-content-end mb-3">
          <button class="btn btn-primary me-2" @click="handlePush">Add Row</button>
         
        </div>
        <h5>Table Info</h5>
        <div class="d-flex mb-3">
         
          <input type="text" v-model="tableInfo.tableName"  class="form-control me-2 mb-2" placeholder="Enter Table Name">
          <input type="number" v-model="tableInfo.tableRow"  class="form-control me-2 mb-2" placeholder="Enter Table Starting Row">
          <input type="number" v-model="tableInfo.tableCol"  class="form-control me-2 mb-2" placeholder="Enter Table Total Columns">
        </div>
        <h5>Summary</h5>
        <div class="d-flex mb-3">
          
          <input type="text" v-model="summaryInfo.summaryName"  class="form-control me-2 mb-2" placeholder="Enter Summary Name">
          <input type="text" v-model="summaryInfo.summaryCellText"  class="form-control me-2 mb-2" placeholder="Enter Acutual Cell Text ">
          <input type="text" v-model="summaryInfo.summaryCol"  class="form-control me-2 mb-2" placeholder="Enter Column Number">
        </div>
        <button class="btn btn-primary" @click="saveTofireBase">Save Template</button>
      </div>
      <h4 class="text-center">Coordinate Table</h4>
      <div class="table-responsive">
        <table class="table table-hover table-striped table-bordered">
          <tbody>
            <tr v-for="(cell, rowindex) in excel2DArray" :key="rowindex">
              <td v-for="(item, colindex) in cell" :key="colindex">{{ item || '-' }} ({{ rowindex + 1 }}, {{ colindex + 1 }})</td>
            </tr>
          </tbody>
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
      excel2DArray: [],
      templateName:"",
      fieldMeta: [{
        name: '',
        row: '',
        col: '',
      
      }],
      tableInfo:{
        tableName:'',
        tableRow:'',
        tableCol:'',
      },
      summaryInfo:{
        summaryName:'',
        summaryCellText:'',
        summaryCol:'',
      }
    }
  },
  methods: {
    handleFileChange(e) {
      const file = e.target.files[0];
      readXlsxFile(file).then((data) => {
        console.log(data);
        this.excel2DArray = data;
      })
    },
    handlePush(){
    this.fieldMeta.push({name:"",type:"",row:"",col:"",cellText:'',});
    },
    saveTofireBase(){
      let data = {template:this.fieldMeta,name:this.templateName,tableInfo:this.tableInfo,summaryInfo:this.summaryInfo}
      db.collection('template').add(data).then((resp)=>{
        console.log(resp);
        this.fieldMeta = [{
        name: '',
        row: '',
        col: '',
      }];
      this.tableInfo={
        tableName:'',
        tableRow:'',
        tableCol:'',
      }
      this.summaryInfo={
        summaryName:'',
        summaryCellText:'',
        summaryCol:'',
      }
      this.$router.push('/');
      }).catch((err)=>{
        console.log(err);
      })
    },
    remove(index){
      if(this.fieldMeta.length == 1) return;
      this.fieldMeta = this.fieldMeta.filter((ele,i)=> i != index)
    }
  }
}

</script>

<style>
body {
  background-color: #f8f9fa;
}

.table {
  background-color: white;
  border-radius: 10px;
  overflow: hidden;
  box-shadow: 0 4px 8px rgba(0, 0, 0, 0.1);
}

.table-hover tbody tr:hover {
  background-color: #f1f1f1;
}
</style>
