<template>
  <div class="wrapper">
    <div class="menu">
      <div class="top-icons">
        <a href="#"><img src="./icons/user.png" style="width:90px;height:90px;"></a>
        <a href="#" class="active"><img src="./icons/menu.png" style="width:34px;height:27px;"><p style="margin-top:17px;">Görevler<i>9</i></p></a>
        <a href="#"><img src="./icons/settings.png" style="width:43px;height:43px;"><p style="color:#787878;margin-top:10px;">Ayarlar</p></a>
      </div>
      <div class="bottom">
        <a href="#"><img src="./icons/close.png" style="width:44px;height:44px;margin:bottom:9px;"><p style="margin:7px 0px 50px; 0px">Kapat</p></a>
      </div>
    </div>
    <div class="content">
      <div class="admin">
        <a href="#"><img src="./icons/refresh.png" style="width: 40px;height: 40px;"></a>
        <p>Görev Yönetim Paneli</p>
      </div>
      
      <div class="tabs">
        <a href="#" @click="selectTab('all')" 
          :class="{ activetab : isActive('all') }">
          <img src="./icons/alltasks.png" style="width: 45px;">
          <p>Tüm Görevler</p>
        </a>
        <a href="#" @click="selectTab('active')" 
          :class="{ activetab : isActive('active') }">
          <img src="./icons/active.png">
          <p>Aktif Görevler</p>
          <i :class="{activebadge : findActiveLengt, 'passivebadge' : !findActiveLengt}">{{findActiveLengt}}</i>
        </a>
        <a href="#" @click="selectTab('completed')" 
          :class="{ activetab : isActive('completed') }">
          <img src="./icons/done.png">
          <p>Biten Görevler</p>
        </a>
      </div> 
      <div class="list" v-show="activeTab == 'all'">
        <div class="search">
          <input type="text" placeholder="Arama.." v-model="search">
          <a @click="isOverlayActive = true">Yeni Görev Ekle <span>+</span></a>
        </div>
        <div class="listcontent">
          <ul>
            <li v-for="(item,index) in searchedTasks" :key="index">
              <div class="listleft">
                <a @click="checkAsComp(item)">
                  <input type="checkbox" class="checkbox">
                </a>
                <div :class="{checkedAsDone : item.isCompleted}">{{item.task}}</div>
              </div>
              <div class="listright">
                <a @click="deleteTask(index)">
                  <img src="./icons/delete.png" style="height: 15px;width: 8px;">
                </a>
                <a @click="moveTask(index)">
                  <img src="./icons/move.png" style="height: 6px;width: 12px;">
                </a>
              </div>  
            </li>
          </ul>
        </div>
        <div class="pickall">
          <p>Hepsini Seç <span> > </span></p>
        </div> 
      </div>
      <div class="list" v-show="activeTab == 'active'">
        <div class="search">
          <input type="text" placeholder="Arama.." v-model="search">
          <a @click="isOverlayActive = true">Yeni Görev Ekle <span>+</span></a>
        </div>
        <div class="listcontent">
          <ul>
            <li v-for="(item,index) in searchedActiveTasks" :key="index">
              <div class="listleft">
                <a @click="checkAsComp(item)"><input type="checkbox" class="checkbox"></a>
                <div :class="{checkedAsDone : item.isCompleted}">{{item.task}}</div>
              </div>
              <div class="listright">
                <a @click="deleteTask(index)"><img src="./icons/delete.png" style="height: 15px;width: 8px;" alt=""></a>
                <a @click="moveTask(index)"><img src="./icons/move.png" style="height: 6px;width: 12px;" alt=""></a>
              </div>  
            </li>
          </ul>
        </div>
        <div class="pickall">
          <p>Hepsini Seç <span> > </span></p>
        </div>
      </div>
      <div class="list" v-show="activeTab == 'completed'">
        <div class="search">
          <input type="text" placeholder="Arama.." v-model="search">
          <a @click="isOverlayActive = true">Yeni Görev Ekle <span>+</span></a>
        </div>
        <div class="listcontent">
          <ul>
            <li v-for="(item,index) in completedTasks" :key="index">
              <div class="listleft">
                <a @click="checkAsComp(item)"><input type="checkbox" class="checkbox"></a>
                <div style="color:#B1B1B1;">{{item.task}}</div>
              </div>
              <div class="listright">
                <a @click="deleteTask(index)"><img src="./icons/delete.png" style="height: 15px;width: 8px;" alt=""></a>
                <a @click="moveTask(index)"><img src="./icons/move.png" style="height: 6px;width: 12px;" alt=""></a>
              </div>  
            </li>
          </ul>
        </div>
        <div class="pickall">
          <p>Hepsini Seç <span> > </span></p>
        </div>  
      </div>
    </div>
    <div class="overlay" id="popup" :class="{overlayActive : isOverlayActive}">
      <div class="addnew">
          <a @click="isOverlayActive = false"><span>×</span></a>
          <h3>Yeni Görev Ekle</h3>
          <textarea cols="30" rows="10" v-model="newTask"></textarea>
          <button @click="addNewTask">EKLE</button>
      </div>
    </div>
    <!-- <div class="menu-responsive">
      <a href="#">
        <img src="./icons/user.png">
      </a>
      <a href="#" class="active">
        <img src="./icons/menu.png">
        <p>Görevler<i>9</i></p>
      </a>
      <a href="#">
        <img src="./icons/settings.png" >
        <p>Ayarlar</p>
      </a>
      <a href="#">
        <img src="./icons/close.png" >
      <p>Kapat</p>
      </a>
    </div>  -->
  </div> 
  
</template>

<script>
export default {
  name: 'app',
  data () {
    return {
      activeTab : 'all',
      tasks : [],
      newTask : '',
      isOverlayActive : false,
      search : ''
    }
  },
  methods : {
    isActive(tabname){
      return this.activeTab === tabname;
    },
    selectTab(tabname){
      this.activeTab = tabname;
    },
    addNewTask(){
      if(this.newTask){
        this.tasks.push({
          task : this.newTask,
          isCompleted : false
        });
        this.isOverlayActive = false;
        this.newTask = "";
      }
    },
    deleteTask(i){
      this.tasks.splice(i,1);
    },
    moveTask(i){
      if(this.tasks.length > (i + 1)){
        let down = this.tasks[i+1].task;
        let up = this.tasks[i].task;
        let downis = this.tasks[i+1].isCompleted;
        let upis = this.tasks[i].isCompleted;
        this.tasks[i].task = down;
        this.tasks[i+1].task = up;
        this.tasks[i].isCompleted = downis;
        this.tasks[i+1].isCompleted = upis;
      }else{
        console.log("yapmayın düşer");
      }
    },
    checkAsComp(item){
      item.isCompleted = !item.isCompleted;
    }
  },
  computed : {
    searchedTasks(){
      return this.tasks.filter((item) => {
        return item.task.match(this.search);
      });
    },
    searchedActiveTasks(){
      return this.activeTasks.filter((item) => {
        return item.task.match(this.search);
      });
    },
    activeTasks(){
      return this.tasks.filter((item) => {
        return item.isCompleted == false;
      });
    },
    completedTasks(){
      return this.tasks.filter((item) => {
        return item.isCompleted == true;
      });
    },
    findActiveLengt(){
      return this.activeTasks.length;
    }
  }
}
</script>

<style>

@media screen and (max-width:990px){
  .menu{
    display: none;
    visibility: hidden;
    position: absolute;
  }
  .content{
    width: 100%;
    height: 100%;
    position: relative;
  }
  .tabs a{
    width: 450px;
  }

}

.menuToogle{
  display: none;
  font-size:56px;
  color:#4b4b4b;
  position: absolute;
  top:450px;
  font-weight: bold;
  cursor:pointer;
}

/* wrappers */
.wrapper{
  display: flex;
  width: 100%;
  height: 100%;
  position: relative;
}
.menu{
  width: 130px;
  background: #343A3D;
}
.content{
  flex: 1;
}

/* menu */
.wrapper .menu{
  display: flex;
  flex-direction: column;
  align-items: center;
  justify-content: space-between;
  padding: 20px 0px;
} 
.wrapper .menu .top-icons a{
  width: 130px;
  display: flex;
  flex-direction: column;
  align-items: center;
  justify-content: center;
  margin-bottom:50px;
  box-sizing: border-box;
  position: relative;
}
.wrapper .menu .top-icons a i {
  width: 30px;
  height: 30px;
  border-radius: 30px;
  background: #ADADAD;
  position: absolute;
  display: flex;
  align-items: center;
  justify-content: center;
  top: -6px;
  right: 28px;
  
}
.wrapper .menu a img{
  width: 60px;
  height: 60px;
}
.wrapper .menu a p {
  color:#fff;
  text-align: center;
}
.wrapper .menu .top-icons .active{
  border-left:9px solid #EE4C3B;
  height: 90px;
  margin-right: 10px;
}
/* menu */
/* content */
  /* content admin */
.admin{
  height: 100px;
  background: #F1F1F1F1;
  border-bottom:1px solid #d3d7da;
  box-sizing: border-box;
  display: flex;
  align-items: center;
  padding-left: 20px;
}
.admin p{
  border:1px solid #cccccc;
  color:#4b4b4b;
  font-size: 16px;
  border-radius: 20px;
  margin-left: 10px;
  padding: 10px;
}
/*** content admin */
/* content tabs */
.tabs{
  height: 78px;
  background: #F1F1F1F1;
  border-bottom:1px solid #d3d7da;
  display: flex;
  align-items: center;
  
}
.tabs a{
  display: flex;
  align-items: center;
  width: 295px;
  height: calc(100% - 1px);
  border-right: 1px solid #DCDCDC;
  box-sizing: border-box; 
  padding-left: 30px;
  position: relative;
  background: #f8f8f8;
  
}
.tabs a img{
  width: 40px;
  height: 40px;
}
.tabs a p{
  padding-left: 20px;
  color:#4b4b4b;
  font-size: 24px;
}
.activetab{
  height:100%!important;
  background: #ffffff!important;
}
.activebadge{
  position: absolute;
  right: 20px;
  top: 15px;
  width: 30px;
  height: 30px;
  border-radius: 25px;
  background:#ff503f;
  color: #fff;
  display: flex;
  align-items: center;
  justify-content: center;
  font-size:15px;
}
.passivebadge{
  position: absolute;
  right: 20px;
  top: 15px;
  width: 30px;
  height: 30px;
  border-radius: 25px;
  background:#DCDCDC;
  color: gray;
  display: flex;
  align-items: center;
  justify-content: center;
  font-size:15px;
}
/*** tabs */

/* list */
.list{
  border:1px solid #DCDCDC;
  margin: 27px 139px 32px 29px;
  border-radius: 5px;
}
.search{
  height: 94px;
  display: flex;
  align-items: center;
  justify-content: space-between; 
  padding: 0px 30px;
  border-bottom:1px solid #DCDCDC;
  box-sizing: border-box;
}
.search input{
  font-size: 22px;
  color:#c1c1c1;
  width: 350px;
  
} 
.search a{
  width: 170px;
  height: 40px;
  color:#fff;
  font-size:16px;
  border-radius: 40px;
  background: #ff503f;
  padding: 15px;
  box-sizing: border-box;
  display: flex;
  align-items: center;
  justify-content: center;
  cursor: pointer;
}
.search a span{
  font-weight: 900;
  padding-left: 8px;
}
/* ------- */

.listcontent ul li{
  height: 89px;
  border-bottom:1px solid #DCDCDC;
  box-sizing: border-box;
  display: flex;
  align-items: center;
  justify-content: space-between;
  padding: 0 30px;
}
.listcontent ul li .listleft,
.listcontent ul li .listright{
  display: flex;
  align-items: center;
  justify-content: center;
}

/* checkbox */

.checkbox{
  -webkit-appearance: none;
	background-color: #fff;
	border: 1px solid #DCDCDC;
	/* box-shadow: 0 1px 2px rgba(0,0,0,0.05), inset 0px -15px 10px -12px rgba(0,0,0,0.05); */
	padding: 9px;
	border-radius: 4px;
	display: inline-block;
	position: relative;
  width: 30px;
  height: 30px;
  box-sizing: border-box;
  cursor: pointer;
}

.checkbox:checked {
	border: 1px solid #DCDCDC;
	color: #b9b9b9;
}

.checkbox:checked:after {
	content: '\2714';
	font-size: 17px;
	position: absolute;
	top: 7px;
	left: 8px;
	color: #b9b9b9;
}

.listcontent ul li .listright a{
  width: 35px;
  height: 35px;
  border: 1px solid #DCDCDC;
  border-radius: 35px;
  box-sizing: 35px;
  display: flex;
  align-items: center;
  justify-content: center;
  margin-left:10px;
  cursor: pointer;
}
.listcontent ul li .listleft div{
  padding-left:40px;
  font-size:22px;
  color: #4b4b4b;
}
.pickall{
  height: 57px;
  border-bottom:1px solid #DCDCDC;
  box-sizing: border-box;
  display: flex;
  align-items: center;
}
.pickall p{
  font-size: 18px;
  color:#ff503f;
  padding-left:32px;
}
.pickall span{
  font-weight: bold;
  padding-left: 20px;
}
.listcontent ul{
  overflow-y: auto;
  max-height: 801px;
}
.listcontent ul li .listright a:first-child:hover{
 background: #ff503f;  
}
.listcontent ul li .listright a:first-child:hover > img{
 filter: brightness(5);  
}
.listcontent ul li .listright a:last-child:hover{
  background: #F1F1F1F1;
}
.listcontent ul li .listright a:last-child:hover > img{
  filter: invert(120%);
}
.addnew{
  top:375px;
  left:400px;
  margin:0 auto;
  padding: 70px 50px 70px 50px;;
  background: #fff;
  border-radius: 5px;
  width: 600px;
  height: 450px;
  position: absolute;
  border-radius: 8px;
  box-sizing: border-box;
  transition: all 5s ease-in-out; 
}
.addnew h3{
  font-size:22px;
  color:#ff503f;
  margin-left:10px;
}
.addnew textarea{
  width: 100%;
  max-width: 100%;
  max-height: 200px;
  padding:30px;
  border: 1px solid #d3d7da;
  box-sizing: border-box;
  margin-top:30px;
  border-radius: 8px;
  font-size: 20px;
  line-height: 32px;
}
.addnew button{
  color:#fff;
  background: #ff503f;
  width: 200px;
  height: 45px;
  border-radius: 50px;
  font-size:18px;
  cursor:pointer;
  display: block;
  margin:0 auto;
  margin-top:30px;
}
.addnew a{
  width: 30px;
  height: 30px;
  border-radius: 15px;
  background: #ff503f;
  color: #fff;
  position: absolute;
  right: 20px;
  top:20px;
  display: flex;
  justify-content: center;
  align-items: center;
  cursor: pointer;
}
.overlay{
  position: fixed;
  top: 0;
  bottom: 0;
  left: 0;
  right: 0;
  background: rgba(0, 0, 0, 0.3);
  transition: opacity 500ms; 
  visibility: hidden; 
  opacity: 0; 
}
.overlayActive {
  visibility: visible;
  opacity: 1;
}
.checkedAsDone{
  font-style: italic;
  text-decoration: line-through;
  color: #B1B1B1!important;
}
</style>
