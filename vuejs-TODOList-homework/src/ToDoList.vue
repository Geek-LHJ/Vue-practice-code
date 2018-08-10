<template>
  <div class="box">
    <div class="box-header">
      <h1>ToDo List</h1>
      <p>添加任务，管理每日计划</p>
    </div>
    <div class="box-input">
      <input placeholder="今天计划完成什么？"
        v-model= "inputValue"
        v-on:keyup.enter= "handleSubmit"/>

      <!-- @click= "..." 等价于 v-on:click= "..." -->
      <button @click= "handleSubmit">添加任务</button>
    </div>
    <div class="box-datalist">
      <div class="list-head">
        <p>任务列表</p>
        <ul>
          <li
            v-bind:class= "{itemActive: all}"
            v-on:click= "handelClickAll">
            全部
          </li>
          <li
            v-bind:class= "{itemActive: over}"
            v-on:click= "handelClickOver">
            已完成
          </li>
          <li
            v-bind:class= "{itemActive: notover}"
            v-on:click= "handelClickNotOver">
            未完成
          </li>
        </ul>
      </div>

      <todo-item
        v-if="diplayList"
        v-for= "(item,index) of list"
        v-bind:key= "index"
        v-bind:content= "item.label"
        v-bind:index= "index"
        v-bind:finished= "item.isFinished"
        v-bind:flag="true"
        @delete= "handelDelete"
        @change= "handelChange"
      >
      </todo-item>

      <todo-item
        v-if="diplayTempover"
        v-for= "(item,index) of tempover"
        v-bind:key= "index"
        v-bind:content= "item.label"
        v-bind:flag="false"
      >
      </todo-item>

      <todo-item
        v-if="diplayTempNotover"
        v-for= "(item,index) of tempNotover"
        v-bind:key= "index"
        v-bind:content= "item.label"
        v-bind:flag="false"
      >
      </todo-item>

        <div class="list-bottom">
          <p>总共 {{list.length}} 条任务,已完成 {{tempover.length}}条，未完成 {{tempNotover.length}}条 </p>
        </div>
        <!-- {{tempNotover}} -->
    </div>
  </div>
</template>

<script>
//引入子组件
import ToDoItem from "./components/todoitem";
//引入localStorage库，保存数据到本地
// import Store from "./store";
export default {
  name: "ToDoList",
  components: { "todo-item": ToDoItem },
  data() {
    return {
      inputValue: "",
      list: [],
      all:true,
      over:false,
      notover:false,
      diplayList:true,
      diplayTempover:false,
      diplayTempNotover:false
    };
  },
  methods: {
    //
    handleSubmit() {
      // alert('submit')
      // this.list 等价于 this.$data.list 的缩写，this 会往data、methods、computed...中寻找；
      if (!this.inputValue) return alert("Please input some content!");
      this.list.push({label:this.inputValue, isFinished:false});
      console.log()
      this.inputValue = "";
    },
    //删除元素
    handelDelete(index) {
      alert("已经删除第" + (index + 1) + "个数据");
      this.list.splice(index, 1);
    },
    //当任务的状态发生变化时，在父组件中保存状态的改变（暂时还有问题，没有实现）
    handelChange(index){
      // console.log(index+":"+this.list[index].isFinished);
      this.list[index].isFinished= !this.list[index].isFinished;

      //保存改变的数据
      // Store.save(list);
    },
    handelClickAll(){
      //让旁边两个 li 的样式变成正常状态
      this.all= true;
      this.over= false;
      this.notover= false;

      //进行数据过滤
      this.diplayList= true;
      this.diplayTempover= false;
      this.diplayTempNotover= false;
      console.log(this.list)

    },
    handelClickOver(){
      //让旁边两个 li 的样式变成正常状态
      this.all= false;
      this.over= true;
      this.notover= false;
      //进行数据过滤
      this.diplayList= false;
      this.diplayTempover= true;
      this.diplayTempNotover= false;
      // this.tempover= this.list.filter(function(item){
      //   return item.isfinished;
      // });
      console.log(this.tempover)
    },
    handelClickNotOver(){
      //让旁边两个 li 的样式变成正常状态
      this.all= false;
      this.over= false;
      this.notover= true;
      //进行数据过滤
      this.diplayList= false;
      this.diplayTempover= false;
      this.diplayTempNotover= true;
      // this.tempNotover= this.list.filter(function (item) {
      //   return !item.isfinished;
      // })
      console.log(this.tempNotover)

    }
  },
  //用于检测数据的变化并存储到  localStorage 当中
  watch: {
    list: {
      handler: function(list) {
        // console.log(val, oldVal);
        //往  localStorage 中存list数据
        //Store.save(list);
      },
      deep: true
    }
  },
  computed:{
    tempover:function(){
      return this.list.filter(function(item){
                return item.isFinished;
              });
    },
    tempNotover:function(){
      return this.list.filter(function(item){
                return !item.isFinished;
              });
    }
  }

};
</script>

<style>
*{margin: 0; padding: 0;}
#app {
  font-family: "Avenir", Helvetica, Arial, sans-serif;
  -webkit-font-smoothing: antialiased;
  -moz-osx-font-smoothing: grayscale;
  text-align: center;
  color: #2c3e50;
  /* margin-top: 20px; */
}
.box{width: 1300px; min-height: 300px; border: 1px solid #eee; margin: 10px auto;}
.box-header{width: 1260px;height: 150px;margin: 20px; background-color: #eee;text-align: center;padding-top: 40px;}
.box-header h1{font-size: 60px;}
.box-input{width: 500px; height: 30px; line-height: 30px; margin: 20px auto;}
.box-input input{width: 400px; height: 30px; text-indent: 1em;}
.box-input button{display: inline-block; width: 80px; height: 35px; line-height: 35px; background-color: rgb(123, 123, 235);border: 0px; border-radius: 5px;color:#fff;font-weight: 400;}
.box-datalist{margin: 35px auto; width: 970px; min-height: 100px; border-radius: 8px;border: 1px solid rgb(102, 116, 197);}
.list-head{height: 55px; background-color: rgb(102, 116, 197);border-radius: 8px 8px 0 0 ;}
.list-head p{display: inline-block;text-indent: 1em;margin-top: 12px;color: white;font-size: 14px;}
.list-head ul{display: block; float: right;height: 30px;line-height: 30px;margin: 8px;border-radius: 8px;border-radius: 5px;}
.list-head ul li{display: block;float: left; padding: 5px 12px; background-color: #fff;border-radius: 5px;cursor: pointer;}

.list-head ul .itemActive{background-color: skyblue;color: #fff;}

.box .list-bottom {height: 50px;}
.box .list-bottom p{height: 50px;line-height: 50px;text-indent: 1em; font-size: 14px;}
</style>
