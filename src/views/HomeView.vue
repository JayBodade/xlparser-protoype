<template>
  <div class="home" style="width: 70%;margin: auto;">
    <!-- <img alt="Vue logo" src="../assets/logo.png"> -->
    <!-- <HelloWorld msg="Welcome to Your Vue.js App"/> -->
    <h5>ALL Templates</h5>
     <div v-if="templateData.length == 0 ">No Data Available</div>
     <table v-else class="table table-hover table-striped table-bordered">
            <thead class="thead-dark">
              <tr>
                <th>Sr. No</th>
                <th>Name</th>
              </tr>
            </thead>
            <tbody>
              <!-- <tr v-for="(tr, rowIndex) in item.table" :key="rowIndex">
                <td v-for="(td, colIndex) in tr" :key="colIndex">{{ td }}</td>
              </tr> -->
              <tr v-for="(item,index) in templateData" :key="index" @click="openModal(item)" data-bs-toggle="modal" data-bs-target="#exampleModal">
                <td>{{ index+1 }}</td>
               
               <td> {{ item.data.name }}</td>
              </tr>
            </tbody>
          </table>
          <!-- Button trigger modal -->
<!-- <button type="button" class="btn btn-primary" data-bs-toggle="modal" data-bs-target="#exampleModal">
  Launch demo modal
</button> -->

<!-- Modal -->
<div class="modal fade" id="exampleModal" tabindex="-1" aria-labelledby="exampleModalLabel" aria-hidden="true">
  <div class="modal-dialog modal-lg">
    <div class="modal-content">
      <div class="modal-header" v-if="modalTemplate">
        <input class="form-control me-2" v-model="modalTemplate.data.name" >
        <button type="button" class="btn-close" data-bs-dismiss="modal" aria-label="Close"></button>
      </div>
      <div class="modal-body">
        <div v-for="(item, index) in  modalTemplate?.data?.template" :key="index" class="d-flex mb-3">
          <input  type="text" v-model="item.name" class="form-control me-2" placeholder="Enter field Name">
          <!-- <select v-model="item.type" class="form-select me-2">
            <option value="Text">Text</option>
            <option value="Table">Table</option>
            <option value="Summary">Summary</option>
          </select> -->
          <input type="text" v-if="item.type=='Summary'" v-model="item.cellText" class="form-control me-2" placeholder="Enter cell text">
          <input type="number" v-else v-model="item.row" class="form-control me-2" placeholder="Enter row number">
          <!-- <input type="number" v-model="item.row" class="form-control me-2" placeholder="Enter row number"> -->
          <input type="number" v-if="item.type === 'Table'" v-model="item.col" class="form-control me-2" placeholder="Enter number of columns">
          <input type="number" v-else v-model="item.col" class="form-control me-2" placeholder="Enter columns number">
          <button  type="button" class="btn btn-primary mx-2" @click="removeRow(item)">Remove</button>
          
        </div>
        <div class="d-flex justify-content-end mb-3">
          <button class="btn btn-primary me-2" @click="addRow">Add Row</button>
         
        </div>
        <h4>Table</h4>
        <div class="d-flex mb-3" v-if="modalTemplate">
         
          <input type="text" v-model="modalTemplate.data.tableInfo.tableName"  class="form-control me-2 mb-2" placeholder="Enter Table Name">
          <input type="number" v-model="modalTemplate.data.tableInfo.tableRow"  class="form-control me-2 mb-2" placeholder="Enter Table Starting Row">
          <input type="number" v-model="modalTemplate.data.tableInfo.tableCol"  class="form-control me-2 mb-2" placeholder="Enter Table Total Columns">
        </div>
        <h4>Summary</h4>
        <div class="d-flex mb-3 " v-if="modalTemplate">
          
          <input type="text" v-model="modalTemplate.data.summaryInfo.summaryName"  class="form-control me-2 mb-2" placeholder="Enter Summary Name">
          <input type="text" v-model="modalTemplate.data.summaryInfo.summaryCellText"  class="form-control me-2 mb-2" placeholder="Enter Acutual Cell Text ">
          <input type="text" v-model="modalTemplate.data.summaryInfo.summaryCol"  class="form-control me-2 mb-2" placeholder="Enter Column Number">
        </div>
        </div>
      <div class="modal-footer">
        <button type="button" class="btn btn-secondary" data-bs-dismiss="modal">Close</button>
        <button type="button" class="btn btn-primary" data-bs-dismiss="modal" @click="saveToFirestore">Save changes</button>
        <button type="button" class="btn btn-danger" data-bs-dismiss="modal" @click="deleteFromStore">Delete</button>
      </div>
    </div>
  </div>
</div>


  </div>
</template>

<script>
// @ is an alias to /src
// import HelloWorld from '@/components/HelloWorld.vue'
import { db } from '@/firebase';
export default {
  name: 'HomeView',
  data(){
    return{
      templateData:[],
      modalTemplate:null,
    }
  },
  components: {
    // HelloWorld
  },
  methods:{
    getFieldMeta() {
            console.log("running this");
            db.collection("template").get()
                .then((querySnapshot) => {
                    querySnapshot.forEach((doc) => {
                        console.log(doc.id, " => ", doc.data());
                        this.templateData.push({ Id: doc.id, data: doc.data() });
                    });
              
                })
                .catch((error) => {
                    console.log("Error getting documents: ", error);
                });
       },
       openModal(item){
        console.log(item);
        this.modalTemplate = item;
       },
      
       saveToFirestore(){

        db.collection('template').doc(this.modalTemplate.Id).update(this.modalTemplate.data).then(()=>{
          console.log("data update success fully");
        }).catch((err)=>{
          console.log(err);
        })
       },
       removeRow(item){
        if (this.modalTemplate && this.modalTemplate.data && this.modalTemplate.data.template) {
    this.modalTemplate.data.template = this.modalTemplate.data.template.filter(ele => ele !== item);
  }
 },
 addRow(){
  this.modalTemplate.data.template.push({ name: '',
        row: '',
        col: '',
        type: 'Text',})
 },
 deleteFromStore(){
   db.collection('template').doc(this.modalTemplate.Id).delete().then(()=>{
    console.log("Deleted Success fully");
    this.templateData = this.templateData.filter((ele)=>ele.Id != this.modalTemplate.Id);
   }).catch((e)=>{
    console.log(e);
   })
 }
       
  },
  created(){

    this.getFieldMeta();
    
  }
}
</script>
