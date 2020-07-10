<template>
  <div id="app">
    <h1>Kanban Board</h1>
  <div class="cont">

  <form style="display:flex;" :class="{'was-validated': wasValidated}" @submit="checkForm" novalidate>
  <input v-model="name" placeholder="Имя проекта" type="text" class="form-control" required>
  <button type="button" class="theme-button" @click="addPlanMin">Сохранить</button>
   </form>
  <button type="button" class="btn btn-primary" data-toggle="modal" data-target="#myModal" style="margin-left: 10px;"> Создать задачу(расширенно) </button> </div>

  <main>
  
<div class="contForCards">

  <div class="Columns">
   <h2>План({{InPlan}})</h2>
    <draggable :arr="arrPlan" group="people" @drop='onDrop($event, "план")' @dragover.prevent @dragenter.prevent>
    <div v-for='(arrPlan, index) in arrPlan' :key='index' class="cardInPlan"  draggable @dragstart='startDrag($event, item)'>
     <p>Задача номер- {{ arrPlan.index }}</p>
    <p>Название - {{ arrPlan.name }}</p>
    <p>Ответственный - {{ arrPlan.nameofmain }}</p>
    <p>Процесс - {{ arrPlan.selected }}</p> 
    <button  type="button" class="theme-button" data-toggle="modal" data-target="#myChangeModal" @click="ChangeInPlan"> Изменить </button>
    <button  type="button" class="theme-button" @click="ToProc(index)"> Уже В процессе </button>
    <button  type="button" class="theme-button" @click="DeletePlan(index)"> Удалить </button></div>
   </draggable>
 </div>
   

  <div class="Columns">
  <h2>В процессе({{InProc}})</h2>
  <draggable :arr="arrInProcess" group="people" @drop='onDrop($event, "процесс")' @dragover.prevent @dragenter.prevent>
  <div v-for='(arrInProcess, index) in arrInProcess' :key='index' class="cardInPlan" draggable @dragstart='startDrag($event, item)'>
    <p>Задача номер- {{ arrInProcess.index }}</p>
    <p>Название - {{ arrInProcess.name }}</p>
    <p>Ответственный - {{ arrInProcess.nameofmain }}</p>
    <p>Процесс - {{ arrInProcess.selected }}</p>
    <p>Старт - {{ arrInProcess.timestart }}</p>
    <button  type="button" class="theme-button" data-toggle="modal" data-target="#myChangeModal" @click="ChangeInProc"> Изменить </button>
    <button  type="button" class="theme-button" @click="ToDone"> Уже Готово </button>
    <button  type="button" class="theme-button" @click="DeleteInProcess(index)"> Удалить </button>
  </div></draggable>
  </div>


  <div class="Columns">
  <h2>Готово({{InDone}})</h2>
    <draggable :arr="arrPlan" group="people" @drop='onDrop($event, "готово")' @dragover.prevent @dragenter.prevent>
    <div v-for='(arrDone, index) in arrDone' :key='index' class="cardInPlan" draggable @dragstart='startDrag($event, item)'>
    <p>Задача номер- {{ arrDone.index }}</p>
    <p>Название - {{ arrDone.name }}</p>
    <p>Ответственный - {{ arrDone.nameofmain }}</p>
    <p>Процесс - {{ arrDone.selected }}</p>
    <p>Старт - {{ arrDone.timestart }}</p>
    <p>Финиш - {{ arrDone.timefinish }}</p>
    <p>Потрачено времени - {{ arrDone.timefinish }}</p>
    <button type="button" class="theme-button" data-toggle="modal" data-target="#myChangeModal" @click="ChangeInDone"> Изменить </button>
    <button type="button" class="theme-button" @click="DeleteDone(index)"> Удалить </button>
    </div></draggable>
  </div>
  </div>
  </main>


   <div class="modal" id="myChangeModal">
    <div class="modal-dialog">
      <div class="modal-content">
      <div class="modal-header">
          <h4 class="modal-title">Создание задачи</h4>
          <button type="button" class="close" data-dismiss="modal">×</button>
        </div>
        <div class="modal-body">
        <form>
        <label for="AboutInout">Описание</label>
        <div>
        <!-- -->
        <input v-model="name" v-for='(arrPlan, index) in arrPlan' :key='index' placeholder={{ this.name }} type="text" class="form-control" id="AboutInput" name="name" required>
       
         <!-- -->
        </div>
       <label>Статус</label>
        <select v-model="selected" id="choose" class="form-control" name="number">
        <option v-for="(option,index) in options" :key='index'>
        {{ option.text }} </option>
        </select>

        <label for="MainManInput">Ответственный</label>
        <div>
         <!-- -->
        <input v-model="nameofmain" placeholder="ФИО ответственного" type="text" class="form-control" id="MainManInput" name="name" required>
        
         <!-- -->
        </div>
        <div v-if="selected==='В работе'">
        <label for="StartDate">Дата начала задания</label>
        <input v-model="timestart" placeholder="Начало проекта" type="datetime-local" class="form-control" id="StartDate" required>
        </div>
         <div v-if="selected==='Готово'">
        <label for="StartDate">Дата начала задания</label>
        <input v-model="timestart" placeholder="Начало проекта" type="datetime-local" class="form-control" id="StartDate" required>
        </div>

        <!-- -->
        <div v-if="selected==='Готово'">
        <label for="StartDate">Дата окончания</label>
         <!-- -->
        <input  v-model="timefinish" placeholder="Планируемое окончание проекта" type="datetime-local" class="form-control" id="EndDate" required>
        </div>
        <!-- -->
        </form>
        </div>
        
        <div class="modal-footer" v-if="CounterPlan===1">
        <button v-if="selected==='План'" type="button" class="btn btn-save" @click="ChangePlanInPlan">Сохранить</button>
        <button v-if="selected==='В работе'" type="button" class="btn btn-save" @click="ChangeProcessInPlan">Сохранить</button>
        <button v-if="selected==='Готово'" type="button" class="btn btn-save" @click="ChangeDoneInPlan">Сохранить</button>
        </div>
        <div class="modal-footer" v-if="CounterProc===1">
        <button v-if="selected==='План'" type="button" class="btn btn-save" @click="ChangePlanInProc">Сохранить</button>
        <button v-if="selected==='В работе'" type="button" class="btn btn-save" @click="ChangeProcessInProc">Сохранить</button>
        <button v-if="selected==='Готово'" type="button" class="btn btn-save" @click="ChangeDoneInProc">Сохранить</button>
        </div>
        <div class="modal-footer" v-if="CounterDone===1">
        <button v-if="selected==='План'" type="button" class="btn btn-save" @click="ChangePlanInDone">Сохранить</button>
        <button v-if="selected==='В работе'" type="button" class="btn btn-save" @click="ChangeProcessInDone">Сохранить</button>
        <button v-if="selected==='Готово'" type="button" class="btn btn-save" @click="ChangeDoneInDone">Сохранить</button>
        </div>
       </div>
    </div>
   </div>



   



  <div class="modal" id="myModal">
    <div class="modal-dialog">
      <div class="modal-content">

        
        <div class="modal-header">
          <h4 class="modal-title">Создание задачи</h4>
          <button type="button" class="close" data-dismiss="modal">×</button>
        </div>
        
        <div class="modal-body">
        <form>
        <label for="AboutInout">Описание</label>
        <div>
        <!-- -->
        <input v-model="name"  placeholder="Имя проекта" type="text" class="form-control" id="AboutInput" name="name" required>
       
         <!-- -->
        </div>
       <label>Статус</label>
        <select v-model="selected" id="choose" class="form-control" name="number">
        <option v-for="(option,index) in options" :key='index'>
        {{ option.text }} </option>
        </select>

        <label for="MainManInput">Ответственный</label>
        <div>
         <!-- -->
        <input v-model="nameofmain" placeholder="ФИО ответственного" type="text" class="form-control" id="MainManInput" name="name" required>
        
         <!-- -->
        </div>
        <div v-if="selected==='В работе'">
        <label for="StartDate">Дата начала задания</label>
        <input v-model="timestart" placeholder="Начало проекта" type="datetime-local" class="form-control" id="StartDate" required>
        </div>
         <div v-if="selected==='Готово'">
        <label for="StartDate">Дата начала задания</label>
        <input v-model="timestart" placeholder="Начало проекта" type="datetime-local" class="form-control" id="StartDate" required>
        </div>

        <!-- -->
        <div v-if="selected==='Готово'">
        <label for="StartDate">Дата окончания</label>
         <!-- -->
        <input  v-model="timefinish" placeholder="Планируемое окончание проекта" type="datetime-local" class="form-control" id="EndDate" required>
        </div>
        <!-- -->
        </form>
        </div>
        
        <div class="modal-footer">
        <button v-if="selected==='План'" type="button" class="btn btn-save" @click="addPlan">Сохранить</button>
          <button v-if="selected==='В работе'" type="button" class="btn btn-save" @click="addInProcess">Сохранить</button>
          <button v-if="selected==='Готово'" type="button" class="btn btn-save" @click="addDone">Сохранить</button>
        </div>

      </div>
    </div>
  
  </div>
  </div>
  
</template>


<script>
import draggable from "vuedraggable";
export default {
  name: 'App',
        components: {
            draggable,
        },
  data(){
    return{
    wasValidated: false,
    InPlan:0,
    Num:1,
    InProc: 0,
    InDone:0,
    CounterPlan:0,
    CounterProc:0,
    CounterDone:0,
    selected: 'План',
    options: [
      { text: 'План', value: 'План' },
      { text: 'В работе', value: 'В работе' },
      { text: 'Готово', value: 'Готово' }
    ],
      arrPlan:[],
      arrInProcess:[],
      arrDone: [],
    }
  },
  methods:{
      checkForm(event) {
      this.wasValidated = true

      let form = event.target
      if (form.checkValidity() === false) {
        event.preventDefault()
        event.stopPropagation()
      }
    },
    addPlan(){
      this.arrPlan.push({
        index: this.Num,
        name: this.name,
        selected: this.selected,
        nameofmain: this.nameofmain,
      })
      this.InPlan=this.InPlan+1,
      this.Num++
    },
    addInProcess(){
      this.arrInProcess.push({
        index: this.Num,
        name: this.name,
        selected: this.selected,
        nameofmain: this.nameofmain,
        timestart: this.timestart,
      })
      this.InProc++,
      this.Num++
    },
    addPlanMin(){
      this.arrPlan.push({
        index: this.Num,
        name: this.name,
        selected: 'План',
      }),
       this.InPlan=this.InPlan+1,
       this.Num++
    },
    addDone(){
      this.arrDone.push({
        index: this.Num,
        name: this.name,
        selected: this.selected,
        nameofmain: this.nameofmain,
        timestart: this.timestart,
        timefinish: this.timefinish
      })
      this.InDone++,
      this.Num++
    },
    DeletePlan(index){
      this.$delete(this.arrPlan, index),
      this.InPlan=this.InPlan-1
    },
    DeleteInProcess(index){
     this.$delete(this.arrInProcess, index),
      this.InProc=this.InProc-1
    },
    DeleteDone(index){
      this.$delete(this.arrDone, index),
      this.InDone=this.InDone-1
    },
    ToProc(index){
      this.InPlan=this.InPlan-1,
      this.arrInProcess.push({
        name: this.name,
        selected: 'В работе',
        nameofmain: this.nameofmain,
        timestart: new Date().toLocaleString(),
      }),

      this.InProc=this.InProc+1,
      this.$delete(this.arrPlan, index)
    },
    ToDone(){
      this.InProc=this.InProc-1,
      this.arrDone.push({
      name: this.name,
      selected: 'Готово',
      nameofmain: this.nameofmain,
      timestart: this.arrInProcess.timestart,
      timefinish: new Date().toLocaleString(),
      }),
      this.InDone=this.InDone+1,
      this.arrInProcess.splice(this.arrInProcess)
    },
    ChangeInPlan(){
      this.CounterPlan++
    },
    ChangeInProc(){
      this.CounterProc++
    },
    ChangeInDone(){
      this.CounterDone++
    },
    ChangePlanInPlan(){
      this.arrPlan.splice(this.arrPlan),
        this.arrPlan.push({
        name: this.name,
        selected: this.selected,
        nameofmain: this.nameofmain,
      })
      this.CounterPlan=this.CounterPlan-1
    },
    ChangeProcessInPlan(){
      this.arrPlan.splice(this.arrPlan),
       this.InPlan=this.InPlan-1,
       this.CounterPlan=this.CounterPlan-1,
       this.arrInProcess.push({
        name: this.name,
        selected: this.selected,
        nameofmain: this.nameofmain,
        timestart: this.timestart,
      })
      this.InProc++
    },
    ChangeDoneInPlan(){
      this.arrPlan.splice(this.arrPlan),
       this.InPlan=this.InPlan-1,
       this.CounterPlan=this.CounterPlan-1,
       this.arrDone.push({
        name: this.name,
        selected: this.selected,
        nameofmain: this.nameofmain,
        timestart: this.timestart,
        timefinish: this.timefinish
      })
      this.InDone++
    },
    ChangePlanInProc(){
      this.arrInProcess.splice(this.arrInProcess),
      this.InProc=this.InProc-1,
      this.CounterProc=this.CounterProc-1,
      this.arrPlan.push({
        name: this.name,
        selected: this.selected,
        nameofmain: this.nameofmain,
      })
      this.InPlan=this.InPlan+1
    },
    ChangeProcInProc(){
      this.arrInProcess.splice(this.arrInProcess),
      this.CounterProc=this.CounterProc-1,
      this.arrInProcess.push({
        name: this.name,
        selected: this.selected,
        nameofmain: this.nameofmain,
        timestart: this.timestart,
      })
    },
    ChangeDoneInProc(){
      this.arrInProcess.splice(this.arrInProcess),
      this.InProc=this.InProc-1,
      this.CounterProc=this.CounterProc-1,
      this.arrDone.push({
        name: this.name,
        selected: this.selected,
        nameofmain: this.nameofmain,
        timestart: this.timestart,
        timefinish: this.timefinish
      })
      this.InDone++
    },
    ChangePlanInDone(){
      this.arrDone.splice(this.arrDone),
      this.InDone=this.InDone-1,
      this.arrPlan.push({
        name: this.name,
        selected: this.selected,
        nameofmain: this.nameofmain,
      })
      this.InPlan=this.InPlan+1,
      this.CounterDone=this.CounterDone-1
    },
    ChangeProcessInDone(){
      this.arrDone.splice(this.arrDone),
      this.InDone=this.InDone-1,
      this.arrInProcess.push({
        name: this.name,
        selected: this.selected,
        nameofmain: this.nameofmain,
        timestart: this.timestart,
      })
      this.InProc++,
      this.CounterDone=this.CounterDone-1
    },
    ChangeDoneInDone(){
      this.arrDone.splice(this.arrDone),
      this.arrDone.push({
        name: this.name,
        selected: this.selected,
        nameofmain: this.nameofmain,
        timestart: this.timestart,
        timefinish: this.timefinish
      }),
      this.CounterDone=this.CounterDone-1
    },
    onSubmit() {
      this.$validator.validateAll()
      
      if (!this.errors.any()) {
        alert('submit')
      }
    }
}
}
</script>

<style>
.dark-theme .cardInPlan{
  background-color:#333333;
  border-bottom:2px solid #6653d9;
  color: white;
  font-size: 15;
  padding: 15px;
  border-radius: 20px;
  display:flex;
  flex-direction:column;
  justify-content:start;
  margin-bottom: 8px;
}
.dark-theme h2{
  color: #9484f2;
}
.dark-theme input{
  background-color:#545454;
  color: white;
  border: none;
}
.dark-theme .modal{
  color: white;
}
.dark-theme form{
  background-color:#636363;
}
.dark-theme .modal-content{
  background-color:#636363;
}
.dark-theme select{
  background-color:#545454;
  color: white;
}
.dark-theme option{
  background-color:#545454;
  color: white;
}
.dark-theme option:hover{
   border:2px solid #6653d9;
}
.cardInPlan{
  background-color:white;
  border-bottom:2px solid #6653d9;
  font-size: 15;
  padding: 15px;
  border-radius: 20px;
  display:flex;
  flex-direction:column;
  justify-content:start;
  margin-bottom: 8px;
}

#app {
  font-family: Avenir, Helvetica, Arial, sans-serif;
  -webkit-font-smoothing: antialiased;
  -moz-osx-font-smoothing: grayscale;
  text-align: center;
  color: #2c3e50;
  margin: 20px;
  padding:20px;
}
h1{
 color: #6653d9;
 margin: 30px;
 margin-top:0px;
 text-align:start;  
 margin-left: 0px;
}
h2{
 color: #6653d9;
 margin: 30px;
}
main{
  padding: 10%;
  
}
.contForCards{
  display:flex;
  flex-wrap: wrap;
  justify-content: space-between;
}
.dark-theme .Columns{
  background-color:#454545;
  border:1px solid #6653d9;
  min-height: 600px;
  margin: 5px;
  border-radius: 20px;
  padding: 8px;
  width: 300px;
}
.Columns{
  width: 300px;
  background-color:#f0f0f0;
  border:1px solid #6653d9;
  min-height: 600px;
  margin: 5px;
  border-radius: 20px;
  padding: 8px;
}
 .theme-button {
  color: #6653d9;
  border: 1px solid #6653d9;
  border-radius:5px;
  margin:2.5px;
}
.theme-button:hover {
  color: #ffffff;
  background-color: #6653d9;
}
.dark-theme .theme-button {
  color: #9484f2;
  border: 1px solid #9484f2;
  background-color: #9484f2;
  color: white;
}
.dark-theme .theme-button:focus,
.dark-theme .theme-button:hover {
  color: #17161a;
  background-color: #9484f2;
}
.cont{
  display: flex;
  justify-content: space-between;
  color: #333333;
  flex-wrap: wrap;
}
@media screen and (max-device-width: 810px ){
  .contForCards{
    width:100%;
    margin-left: 20%;
    display: flex;
    flex-direction: column;
    justify-content: center;
  }
}
</style>

