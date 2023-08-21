<template>
  <QLDSSV/>
</template>

<script>
import QLDSSV from './components/QLDSSV.vue'

export default {
  name: 'App',
  components: {
    QLDSSV
  }
}
</script>

<style>
.App {
  text-align: center;
}

body{
  margin: 0 10em;
  width: 50%;
  font-size: large;
}

table, th, td{
  border: 2px solid black;
  background-color: rgba(0, 157, 255, 0.711);
  color: #FFF;
}

table{
  width: 1000px;
  border-collapse: collapse;
  
}

th, td{
  padding: 10px;
  height: 30px;
}

th{
  background-color: gray;
}

td{
  padding: 20px;
}

h1{
  text-align: center;
}

.search{
  margin-bottom: 20px;
  
}

input{
  height: 40px;
  margin-left: 20px;
}

button{
  background-color: rgba(0, 157, 255, 0.711);
  color: #FFF;
  height: 30px;
  width: 100px;
  font-size: medium;
}

#txtTuKhoa{
  border: 2px solid rgba(0, 157, 255, 0.711);
}

#form{
  border: 1px solid rgba(0, 157, 255, 0.711);
  margin-top: 20px;
  width: 1000px;
  margin-bottom: 50px;
}

.function{
  margin-bottom: 20px;
}

.form_input{
  margin: 0 auto;
  width: 100%;
  text-align: center;
}

label {
  padding: 5px 12px ;
  width: 150px;
  margin: 15px 0;
  background-color: rgb(216, 208, 203);
  display: inline-block;
}

.form_input input, select{
  margin-left: 20px;
}

#txtNgaySinh{
  height: 40px;
  width: 230px;
}

#rdbGioiTinh label{
  width: 90px !important;
  height: 35px;
}


#drpKhoa{
  width: 230px;
}

a{
  color: #FFF;
  margin-right: 10px;
}

select{
  height: 30px;
  width: 170px;
  font-size: large;
}

</style>
