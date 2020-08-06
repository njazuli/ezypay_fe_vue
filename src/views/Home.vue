<template>
  <div class="container">
    <div class="row my-2 justify-content-center">
      <div class="col-12">
        <h2>List of created subscription</h2>
      </div>
    </div>
    <div class="card my-2 my-md-2" v-if="noresponse">
      <div class="card-body">
        <h3>No subscription created yet!</h3>
      </div>
    </div>
    <div class="card my-2 my-md-2" v-else v-for="items in datareturned" :key="'data'+items._id">
      <div class="card-body">
        <h3>{{items.subscriptions_type}}</h3>
        <div class="w-100 d-flex flex-column align-items-center justify-content-center flex-md-row">
          <div class="col-12 col-md-6">
              <p>Amount charged : <b>${{items.amount_charged}}</b></p>
              <p>Subscription from <b>{{items.subscriptions_start_date}}</b><br> to <b>{{items.subscriptions_end_date}}</b> </p>
              <p>Status : <span v-bind:class="{'text-success': items.status == 'Active',  'text-danger': items.status == 'Expired'}">{{items.status}}</span></p>
          </div>
          <div class="col-12 col-md-6">
            <p class="mb-1">List of invoices date: </p>
            <ul>
              <li v-for="(invoicelist,index) in items.invoices_date" :key="'invoice'+index"><b>{{invoicelist}}</b></li>
            </ul>
          </div>
        </div>
      </div>
    </div>
  </div>
</template>

<script>
/* eslint-disable */
import axios from "axios";
export default {
  name: 'Home',
  data() {
      return {
        datareturned: [],
        status: "",
        noresponse: true
      }
  },
  methods: {
    getDetails(){
      var self = this;
          try {
              axios
              .get(
                  "http://localhost:3000/subscribers"
              )
              .then(function(response) {
                if(Array.isArray(response.data) && response.data.length){
                  self.processData(response.data);
                  self.noresponse = false;
                }else{
                  self.noresponse = true;
                }
                
              })
              .catch(function(error) {
                  self.noresponse = true;
              });
          } catch (e) {
              // console.log(e);
              self.noresponse = true;
          }
    },
    processData(obj){
      let todaysdate = new Date().getTime();
      // console.log(JSON.stringify(obj,null,2))

      obj.sort(function(a,b){
        return new Date(b.createdDate) - new Date(a.createdDate);
      });

      obj.forEach(items => {
        var parts = items.subscriptions_end_date.split(/\//);
        var mydate = parts[1] + '/' + parts[0] + '/' + parts[2];
        var convertedendate = new Date(mydate).getTime();
        // console.log("parts " + parts)
        // console.log("convertedendate " + convertedendate)
        if(todaysdate <= convertedendate){
          items.status = "Active";
        }else{
          items.status = "Expired";
        }
      });

      this.datareturned = obj;
    },
    GetFormattedDate(adate) {
        var a = new Date(adate);
        var day = a.getDate();
        var month = a.getMonth()+1;
        var year = a.getFullYear();

        
        if(day < 10){
            day = '0'+day;
        }
        if(month < 10){
            month = '0'+month;
        }

        return day + "/" + month + "/" + year;
    }
  },
  mounted(){
    this.getDetails();
  }
}
</script>
<style scoped>
ul {
  list-style-type: none !important;
  padding: 0;
  margin-bottom: 5px;
}
</style>