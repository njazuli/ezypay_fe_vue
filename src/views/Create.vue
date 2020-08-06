<template>
  <div class="container-fluid">
        <!-- <b-modal id="success" v-if="showsuccess" title="successmodal">
            <p class="my-4 text-success">Success - Data successfully inserted into db. Go to List page to view.</p>
        </b-modal>
        <b-modal id="error" v-if="showerr" title="errormodal">
            <p class="my-4 text-danger">Success - Data successfully inserted into db. Go to List page to view.</p>
        </b-modal> -->
      <div class="row mb-2">
          <div class="col-12">
              <h3>Create A Subscription</h3>
          </div>
      </div>
      <div class="row d-flex justify-content-center">
          <div class="col-12">
              <div style="width:300px;">

              </div>
              <p v-if="errors.length">
                <b class="red_font">Please correct the following error(s):</b>
                <ul>
                    <li v-for="(error,index) in errors" :key="'error'+index" class="red_font">{{ error }}</li>
                </ul>
            </p>
          </div>
          <div class="col-12 col-md-7">
                <form @submit="checkForm">
                    <div class="form-group text-left">
                        <div class="row">
                            <div class="col-12 col-md-4">
                                <label for="amountcharges">Amount charged ($)</label>
                            </div>
                            <div class="col-12 col-md-8">
                                <input type="number" class="form-control" id="amountcharges" v-model="form.amountcharges" min="1">
                            </div>
                        </div>
                    </div>
                    <div class="form-group text-left">
                        <div class="row">
                            <div class="col-12 col-md-4">
                                <label for="typeofsubscription">Type of subscription</label>
                            </div>
                            <div class="col-12 col-md-8">
                                <select class="form-control" id="typeofsubscription" v-model="form.typeofsubscription" @change="checkTypeofSubs()">
                                    <option>Daily</option>
                                    <option>Weekly</option>
                                    <option>Monthly</option>
                                </select>
                            </div>
                        </div>
                    </div>
                    <div class="form-group text-left" v-if="showDay">
                        <div class="row">
                            <div class="col-12 col-md-4">
                                <label for="dayofsubscription">Choose Day of invoices will be issued on</label>
                            </div>
                            <div class="col-12 col-md-8">
                                <select class="form-control" id="dayofsubscription" v-model="form.dayofsubscription">
                                    <option>Monday</option>
                                    <option>Tuesday</option>
                                    <option>Wednesday</option>
                                    <option>Thursday</option>
                                    <option>Friday</option>
                                    <option>Saturday</option>
                                    <option>Sunday</option>
                                </select>
                            </div>
                        </div>
                    </div>
                    <div class="form-group text-left" v-if="showDate">
                        <div class="row">
                            <div class="col-12 col-md-4">
                                <label for="dateofsubscription">Choose Date of invoices will be issued on</label>
                            </div>
                            <div class="col-12 col-md-8">
                                <select class="form-control" id="dateofsubscription" v-model="form.dateofsubscription">
                                    <option v-for="n in 30" :key="'n-'+n">{{n}}</option>
                                </select>
                            </div>
                        </div>
                    </div>
                    <div class="form-group text-left">
                        <div class="row">
                            <div class="col-12">
                                <label for="typeofsubscription">Subscription date</label>
                            </div>
                            <div class="col-12">
                                <HotelDatePicker
                                :maxNights="90" :i18n="ptBr" @check-in-changed="updateStartDate" @check-out-changed="updateEndDate">
                                </HotelDatePicker>
                            </div>
                        </div>
                    </div>
                    <button type="submit" class="btn btn-primary">Submit</button>
                </form>
          </div>
      </div>
      <!-- Modal -->
    <div v-if="showsuccess">
        <transition name="modal">
        <div class="modal-mask">
            <div class="modal-wrapper">
            <div class="modal-dialog">
                <div class="modal-content">
                <div class="modal-header">
                    <button type="button" class="close" @click="closeModal()">
                    <span aria-hidden="true">&times;</span>
                    </button>
                </div>
                <div class="modal-body">
                    <p class="my-4 text-success">Success - Data successfully inserted into db. Go to List page to view.</p>
                </div>
                </div>
            </div>
            </div>
        </div>
        </transition>
    </div>
  </div>
</template>

<script>
/* eslint-disable */
import HotelDatePicker from 'vue-hotel-datepicker'
import axios from "axios";
  export default {
    data() {
      return {
          errors: [],
          showDay: false,
          showDate: false,
          showsuccess: false,
          showerr: false,
          form: {
            amountcharges: "",
            typeofsubscription: "",
            dayofsubscription: "",
            dateofsubscription: "",
            startdate: "",
            enddate: ""
          },
          ptBr: {
            night: 'Day',
            nights: 'Day',
            'day-names': ['Sun', 'Mon', 'Tue', 'Wed', 'Thu', 'Fri', 'Sat'],
            'check-in': 'Start',
            'check-out': 'End',
            'month-names': ['Jan', 'Feb', 'Mar', 'Apr', 'May', 'June', 'Jul', 'Aug', 'Sep', 'Oct', 'Nov', 'Dec'],
        }
      }
    },
    components: {
        HotelDatePicker
    },
    methods: {
        checkTypeofSubs(){
            if(this.form.typeofsubscription == "Weekly"){
                this.showDay = true;
                this.showDate = false;
            }else if(this.form.typeofsubscription == "Monthly"){
                this.showDay = false;
                this.showDate = true;
            }else{
                 this.showDay = false;
                this.showDate = false;
            }
        },
        updateStartDate(date){
            this.form.startdate = date
        },
        updateEndDate(date){
             this.form.enddate = date
        },
        closeModal(){
            this.showsuccess = false;
        },
        checkForm(evt){
            this.errors = [];

            if (!this.form.amountcharges) {
                this.errors.push('Amount charged required.');
            }

            if (!this.form.typeofsubscription) {
                this.errors.push('Type of subscription required.');
            }

            // if (!this.form.dayofsubscription || !this.form.dateofsubscription) {
            //     this.errors.push('Day/Date of subscription required.');
            // }

            if (!this.form.startdate) {
                this.errors.push('Start date of subscription is required.');
            }

            if (!this.form.enddate) {
                this.errors.push('End date of subscription is required.');
            }
            

            evt.preventDefault();

            if (this.form.amountcharges && this.form.typeofsubscription  && this.form.startdate && this.form.enddate) {
               this.processTheForm();
            }
            
        },
        processTheForm(){
            var startdate = this.form.startdate;
            var enddate = this.form.enddate;
            var invoicesdate = [];


            //daily - list all days in invoicesdate
            if(this.form.typeofsubscription == "Daily"){
                var getDates = function(startDate, endDate) {
                    var dates = [],
                        currentDate = startDate,
                        addDays = function(days) {
                            var date = new Date(this.valueOf());
                            date.setDate(date.getDate() + days);
                            return date;
                        };
                    while (currentDate <= endDate) {
                        dates.push(currentDate);
                        currentDate = addDays.call(currentDate, 1);
                    }
                    return dates;
                };

                var dates = getDates(new Date(startdate), new Date(enddate)); 

                dates.forEach(element => {
                    invoicesdate.push(this.GetFormattedDate(element));
                });

            }else if(this.form.typeofsubscription == "Weekly"){
                var days = ['Sunday', 'Monday', 'Tuesday', 'Wednesday', 'Thursday', 'Friday', 'Saturday'];

                var getDates = function(startDate, endDate, chosenday) {
                    var dates = [],currentDate = startDate,
                        addDays = function(days) {
                            var date = new Date(this.valueOf());
                            date.setDate(date.getDate() + days);
                            return date;
                        };
                    while (currentDate <= endDate) {
                        if(days[currentDate.getDay()] == chosenday){
                            dates.push(currentDate);
                        }
                        
                        currentDate = addDays.call(currentDate, 1);
                    }
                    return dates;
                };

                var dates = getDates(new Date(startdate), new Date(enddate),this.form.dayofsubscription); 

                dates.forEach(element => {
                    invoicesdate.push(this.GetFormattedDate(element));
                });

            }else if(this.form.typeofsubscription == "Monthly"){
                var getDates = function(startDate, endDate , chosendate) {
                    var dates = [],
                        currentDate = startDate,
                        addDays = function(days) {
                            var date = new Date(this.valueOf());
                            date.setDate(date.getDate() + days);
                            return date;
                        };
                    while (currentDate <= endDate) {
                        if(currentDate.getDate() == chosendate){
                            dates.push(currentDate);
                        }
                        
                        currentDate = addDays.call(currentDate, 1);
                    }
                    return dates;
                };

                var dates = getDates(new Date(startdate), new Date(enddate),this.form.dateofsubscription); 

                dates.forEach(element => {
                    invoicesdate.push(this.GetFormattedDate(element));
                });
            }

            // set postbody
            var postbody = {
                "amount_charged": this.form.amountcharges,
                "subscriptions_type": this.form.typeofsubscription,
                "subscriptions_start_date": this.GetFormattedDate(this.form.startdate),
                "subscriptions_end_date": this.GetFormattedDate(this.form.enddate),
                "invoices_date": invoicesdate
            }
            
            // console.log(JSON.stringify(postbody,null,2))
            // post
            var self = this;
                try {
                    axios
                    .post(
                        "http://localhost:3000/subscribers",
                        postbody
                    )
                    .then(function(response) {
                        // console.log("resultdate" + JSON.stringify(response, null, 2));
                        self.showsuccess = true;
                    })
                    .catch(function(error) {
                        // handle error
                        // console.log(error);
                        self.showerr = true;
                    });
                } catch (e) {
                    // console.log(e);
                    self.showerr = true;
                }

                //Iterate through each object field, key is name of the object field`
                Object.keys(self.form).forEach(function(key,index) {
                    self.form[key] = '';
                });

                self.form.startdate = "";
                self.form.endDate = "";
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
        },
        parseDate(str_date) {
            return new Date(Date.parse(str_date));
        }
    }
  }
</script>

<style scoped>
.red_font{
    color: red;
}
li {
    list-style-type: none;
}
.modal-mask {
  position: fixed;
  z-index: 9998;
  top: 0;
  left: 0;
  width: 100%;
  height: 100%;
  background-color: rgba(0, 0, 0, .5);
  display: table;
  transition: opacity .3s ease;
}

.modal-wrapper {
  display: table-cell;
  vertical-align: middle;
}
</style>