<template>
  <div class="card text-center m-3">
    <h2>
    <ul v-for="i in so_colrow" :key="i">
      <li v-for="index in so_colrow" :key="index" > 
        <div v-if="sample[i-1][index-1] < 10">
          <a v-if="ket_qua[i-1][index-1] == false" :style="{color: ' #f70505'}"> 0{{ sample[i-1][index-1] }}</a>
          <a v-else>0{{ sample[i-1][index-1] }}</a>
        </div>
        <div v-if="sample[i-1][index-1] >= 10">
          <a v-if="ket_qua[i-1][index-1] == false" :style="{color: ' #f70505'}">{{ sample[i-1][index-1] }}</a>
          <a v-else>{{ sample[i-1][index-1] }}</a>
        </div>
      </li>
    </ul>
    </h2>

  </div>
  <div>
    <select id="ddlViewBy">
      <option value="1">Hitori 4 x 4</option>
      <option value="2">Hitori 5 x 5</option>
      <option value="3">Hitori 7 x 7</option>
      <option value="4">Hitori 8 x 8</option>
      <option value="5">Hitori 9 x 9</option>
      <option value="6">Hitori 10 x 10</option>
      <option value="7">Hitori 15 x 15</option>
      <option value="8">Hitori 20 x 20</option>
      <option value="9">Hitori 25 x 25</option>
    </select>
    <button @click="changeName">Load Hitori</button> 
    <p></p>
    <select id="idphuongphap">
      <option value="CC">Chains & Cycles</option>
      <option value="CE">Connectivity Encoding</option>
    </select>
    <button @click="CCEncoding">Giai Hitori</button> 
    <p></p>
  </div>
  <div>
    <div>Ket qua: {{s_unsat}}</div>
    <div>So bien: {{num_var}}</div>
    <div>So menh de: {{num_clauses}}</div>
    <div>Thoi gian chay: {{time_run}}</div>
  </div>
</template>

<script>
//import axios from 'axios'
import samples from './samples.js'
 
export default {
  name: "post-request-async-await",
  data() {
    return {
      sample: samples[0],
      postId: null,
      s_unsat: null,
      num_clauses: null,
      num_var: null,
      time_run: null,
      ket_qua:null,
      so_colrow:4,
      methd: "CC"
    };
  },
  async created() {
    // POST request using fetch with async/await   
    const requestOptions = {
      method: "POST",
      headers: { "Access-Control-Allow-Origin": "*", "Content-Type": "application/json;charset=UTF-8" },
      body: JSON.stringify({"rows": 4,"cols": 4,"method": "CC","data": [[1, 2, 3, 4],[2, 3, 4, 1],[3, 4, 1, 2],[4, 1, 2, 1]]})
    };
    console.log(requestOptions)
    const response = await fetch("http://localhost:8000/hitori-solver", requestOptions);
    const datas = await response.json();
    this.postId = datas;  
    if(datas.ok == true){
      this.s_unsat = "SATIFIABLE";
      this.num_clauses = datas.number_of_clauses;
      this.num_var = datas.number_of_variables;
      this.time_run = datas.running_time;
      this.ket_qua = datas.result;
      }
    else {this.s_unsat = "UNSATIFIABLE";}
  },
  methods: { 
    changeName() { 
    var e = document.getElementById("ddlViewBy");
    if(e.value=="1") {this.so_colrow = 4;}
    if(e.value=="2") {this.so_colrow = 5;}
    if(e.value=="3") {this.so_colrow = 7;}
    if(e.value=="4") {this.so_colrow = 8;}
    if(e.value=="5") {this.so_colrow = 9;}
    if(e.value=="6") {this.so_colrow = 10;}
    if(e.value=="7") {this.so_colrow = 15;}
    if(e.value=="8") {this.so_colrow = 20;}
    if(e.value=="9") {this.so_colrow = 25;}
    this.sample = samples[e.value-1];
    }, 
    //printLogs(newValue, oldValue) { 
    //console.log("newValue", newValue); 
    //console.log("oldValue", oldValue); 
    //},
    
    CCEncoding() {
      var ef = document.getElementById("idphuongphap");
      this.methd = ef.value;
      const requestOptions = {
      method: "POST",
      headers: { "Access-Control-Allow-Origin": "*", "Content-Type": "application/json;charset=UTF-8" },
      body: JSON.stringify({"rows": this.so_colrow,"cols": this.so_colrow,"method": this.methd,"data": this.sample})
      };
      console.log(requestOptions)
      fetch('http://localhost:8000/hitori-solver', requestOptions)
        .then(async response => {
          const datas = await response.json();
          console.log(datas);
          // check for error response
          if (!response.ok) {
            // get error message from body or default to response status
            const error = (datas && datas.message) || response.status;
            return Promise.reject(error);
          }
          this.postId = datas;  
          if(datas.ok == true){
            this.s_unsat = "SATIFIABLE";
            this.num_clauses = datas.number_of_clauses;
            this.num_var = datas.number_of_variables;
            this.time_run = datas.running_time;
            this.ket_qua = datas.result;
          }
          else {this.s_unsat = "UNSATIFIABLE";}
        })
        .catch(error => {
          this.errorMessage = error;
          console.error('There was an error!', error);
        });      
    }, 
  },  
};

</script>

<!-- Add "scoped" attribute to limit CSS to this component only -->
<style scoped>
h3 {
  margin: 40px 0 0;
}
ul {
  list-style-type: none;
  padding: 0;
}
li {
  display: inline-block;
  margin: 0 10px;
}
a {
  color: #42b983;
}
</style>
