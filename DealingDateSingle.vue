<template>
	<div >
        <select class="form-control online-select" style="width:25%" name="" id="day" v-model="day" @change="emitChange">
            <option value="0" >{{  date_name.day }}</option>

            <template v-for="day,index in day_range">
                <option :value="day">{{ ('0' + day).slice(-2) }}</option>
  
            </template>         
        </select> 
        <select class="form-control online-select col-xs-6"  style="width:30%" name="" id="month" v-model="month" @change="emitChange">
             <option value="0" >{{  date_name.month }}</option>
            <option v-for="i in 12" :key="i-1" :value="moment().month(i-1).format('MM')"  >{{ moment().month(i-1).format('MMM')}}</option>
           
        </select>
         <select  class="form-control online-select col-xs-4" style="width:45%" name="" id="year" v-model="year" @change="emitChange">
             <option value="0">{{  date_name.year }}</option>

            <template v-for="year,index in year_range">
                <option :value="year">{{ year }}</option>
            </template> 
        </select>
    </div>

</template>

<script>

var moment = require('moment');
var _ = require('lodash');
window.$ = window.jQuery = require('jquery');

export default {
    data() {
         return {
            moment,
            date_name:{
                year:'Year',
                month:'Month',
                day:'Day',
            },
            default_date: "",
            day:"",
            month:"",
            year:""
        };
    },
    props: ['modelUpdate','modelValue','language'],

    created(){
       this.setLocale();
    },
    mounted() {
        var vm = this;
        this.initDate();
    
        
    },
    methods:{
        setLocale(){
            if(this.language === 'cn'){
             
                const date_label = {
                        year:'年',
                        month:'月',
                        day:'日',
                    }
                this.date_name = date_label;   
            
            }else if(this.language === 'jp'){
        
                 const date_label = {
                        year:'年',
                        month:'月',
                        day:'日',
                }
                this.date_name = date_label;   
            }

        },
        initDate(){
            var vm = this;
            if(this.modelValue != null && this.modelValue != "0-0-0"){
                this.setDefaultValue();
            }else{
                this.setZero();
                this.$nextTick(function(){
                    vm.emitChange();
                });
            }
        },
        emitChange() {
            var vm =  this;
            var date = moment(this.full_date).format('YYYY-MM-DD');

            if(moment(date).format('YYYY-MM-DD') === "Invalid date"){

               date = null;
            }
            this.$nextTick(function(){
                vm.$emit('change-date',{ data:date ,id:this.modelUpdate});
            });
        },
        setTodayDate(){
            this.day = moment().format('DD');
            this.month = moment().format('MM');
            this.year = moment().format('YYYY');
        },
        setZero(){
            this.day = "0";
            this.month = "0";
            this.year = "0";
        },
        setDefaultValue(){
            this.default_date = this.modelValue;
            var date = moment(this.default_date).format('YYYY-MM-DD');
             this.default_date = date;
            var date_split = date.split("-");
            if(moment(date).format('YYYY-MM-DD') === "Invalid date"){
                this.setZero();
            }else{
              this.year   = date_split[0];
              this.month  = date_split[1];
              this.day    = parseInt(date_split[2]);
            }
            this.emitChange();

        }
    },
    watch: {
        "modelValue":function(){
            this.initDate();
        }
    },
    computed: {
       
        full_date(){
            var full_date = this.year +'-' + this.month + '-' + ('0' + this.day).slice(-2);

            return full_date;
        },
        year_range(){
            var this_year  = moment().format('Y');
            return _.range(this_year,1900);
        },
        month_range(){
            return _.range(1, 13);
        },
        day_range(){

            if(this.month === "02"){
                if(this.year % 4 == 0){
                   return _.range(1, 30);
                }else{
                   return _.range(1, 29);
                }
            }
            else if(this.month === "04" || this.month === "06" || this.month === "09" || this.month === "11"){
                return _.range(1, 31);
            }
            else{
                return _.range(1, 32);
            }
        }
    }
}
</script>
