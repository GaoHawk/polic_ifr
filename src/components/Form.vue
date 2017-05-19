<template>
 <el-form :model="dynamicValidateForm" ref="dynamicValidateForm" label-width="90px" class="demo-dynamic" style="width:600px;">
  <el-form-item
    prop="polic"
    label="警种"
    :rules="[
      { required: true, message: '请选择警种', trigger: 'blur' },
    ]"
  >
  <el-select v-model="dynamicValidateForm.polic" placeholder="请选择警种"
    style="float:left;width:34%;"
    >
    <el-option label="刑侦民警" value="1"></el-option>
    <el-option label="社区民警" value="2"></el-option>
    <el-option label="治安民警" value="3"></el-option>
    <el-option label="巡逻民警" value="4"></el-option>
    <el-option label="特警" value="5"></el-option>
    <el-option label="辅警" value="6"></el-option>
    <el-option label="交警" value="7"></el-option>
  </el-select>
  </el-form-item>

  <el-form-item label="姓名" prop="name"
    :rules="{
      required:true, message:'姓名不能为空', trigger:'blur'  
    }"
  >
    <el-input v-model="dynamicValidateForm.name"></el-input>
  </el-form-item>

  <el-form-item label="联系方式" prop="phone"
    :rules="[
      { required:true, message:'联系方式不能为空', trigger:'blur' },
      { type:'number',message:'请输入正确联系方式' }
    ]"
  >
    <el-input v-model="dynamicValidateForm.phone"></el-input>
  </el-form-item>

  <el-form-item label="编号" prop="number"
    :rules="[
      { required:true, message:'编号不能为空', trigger:'blur' },
      { type:'number',message:'编号必须为数字' }
    ]"
  >
    <el-input v-model="dynamicValidateForm.number"></el-input>
  </el-form-item>


  <el-form-item
    class="custom-width"
    v-for="(form, index) in dynamicValidateForm.form"
    :label="'巡逻路线' + index"
    :key="form.key"
    :prop="'form.'+index+'.regionLeft'"
    :rules="{
      required: true, message: '巡逻路线不能为空', trigger: 'blur'
    }"
  >
    <el-select v-model="dynamicValidateForm.form[index].regionLeft" placeholder="请选择巡逻区域" 
    style="float:left;width:34%;"
    @change="changePat(index)">
      <el-option label="八一路" value="1"></el-option>
      <el-option label="洪山路" value="2"></el-option>
      <el-option label="姚家岭路-水果湖路" value="3"></el-option>
      <el-option label="公正路-白鹭街" value="4"></el-option>
      <el-option label="东湖西路-东湖路-白鹭街东" value="5"></el-option>
      <el-option label="洪山侧路" value="6"></el-option>
      <el-option label="天鹅路" value="7"></el-option>
      <el-option label="惠明路-东三路" value="8"></el-option>
      <el-option label="水果湖横路-东一路" value="9"></el-option>
      <el-option label="中北路" value="10"></el-option>
      <el-option label="沙湖大道" value="11"></el-option>
    </el-select>
    <el-form-item
      class="custom-width"
      style="width:62%;float:right;"
      :label="'巡逻点' + index"
      :key="form.key"
      :prop="'form.'+index+'.regionRight'"
      :rules="{
        required: true, message: '巡逻点不能为空', trigger: 'blur'
      }"
    >
    <el-select 
    style="width:51%;"
    class="right"
    v-model="dynamicValidateForm.form[index].regionRight" 
    placeholder="请选择巡逻点"
    @change="changeValue(index)">
      <el-option v-for="item in dynamicValidateForm.form[index].selectOps" :label="item.label" :value="item.value"></el-option>
    </el-select>
  <el-button @click.prevent="removeDomain(form)">删除</el-button>
    </el-form-item>

  </el-form-item>
  <el-form-item>
    <el-button type="primary" @click="submitForm('dynamicValidateForm')">提交</el-button>
    <el-button @click="addDomain">新增域名</el-button>
    <el-button @click="resetForm('dynamicValidateForm')">重置</el-button>
  </el-form-item>
</el-form>
</template>
<script>

export default {
  beforeCreate(){
        var uid = window.location.search;
        console.log(uid);
        var userId = uid.split('=')[1]
        this.$http.get('http://localhost:8080/getUserInfo',{
           params:{
             userid:Number(userId)
           }
        }).then(response => {
           console.log(response.data);
           console.log(typeof (response.data.usertype +1))
           this.dynamicValidateForm.polic = (response.data.usertype +1).toString();
           this.dynamicValidateForm.name = response.data.userName;
           this.dynamicValidateForm.phone = Number(response.data.phone);
           this.dynamicValidateForm.number = Number(response.data.policeNumber);
           let patroStr = response.data.patrolids;
           let patroArr = patroStr.substr(1,patroStr.length-2).split(',');
           console.log(patroArr);
           let patroNames = response.data.patrolnames;
           let pNameArr = patroNames.substr(1,patroNames.length-2).split(',');
           console.log(pNameArr);
           let patroTypes = response.data.patrolids_type;
           let TypeArr = patroTypes.substr(1,patroTypes.length-1).split(',');
           console.log(TypeArr);
           for(let i = 0;i<patroArr.length;i++){
               var newStr = patroArr[i];
               console.log(newStr.substr(0,newStr.length-1))
               this.dynamicValidateForm.form.push({
                  regionLeft:TypeArr[i],
                  key: Date.now(),
                  regionRight:pNameArr[i],
                  selectOps:[]
              })
           }
           var selectedBtn = document.querySelectorAll('.right.el-select');
           console.log(selectedBtn);
        },response => {
          console.log(response);
        })
  },
  name: 'hello',
  data () {
    return {
       dynamicValidateForm:{
           polic:'',
           name:'',
           phone:'',
           number:'',
           form:[]
       },


    };
  },
  methods:{
     submitForm(formName) {
        this.$refs[formName].validate((valid) => {
          if (valid) {
            alert('submit!');
          } else {
            console.log('error submit!!');
            return false;
          }
        });
      },
      resetForm(formName) {
        this.$refs[formName].resetFields();
      },
      removeDomain(item) {
        var index = this.dynamicValidateForm.form.indexOf(item)
        if (index !== -1) {
          this.dynamicValidateForm.form.splice(index, 1)
        }
      },
      addDomain() {
        this.dynamicValidateForm.form.push({
             regionLeft:'',
             key: Date.now(),
             regionRight:'',
             selectOps:[]
        })
      },
      clickSelect(){
         console.log('click');
      },
      changeValue(index){
        var patralP = this.dynamicValidateForm.form[index].regionRight;
        console.log(patralP);
      },
      changePat(index){
        var routeType= this.dynamicValidateForm.form[index].regionLeft;
        console.log(routeType);
        var currentForm = this.dynamicValidateForm.form[index];
        currentForm.selectOps = [];
        currentForm.regionRight = '';
        this.$http.get('http://localhost:8080/getPatrol?type='+routeType,{
    
        }).then(response => {
           console.log(response.data.patrolList);
           let patrolList = response.data.patrolList;
           for(let i = 0;i<patrolList.length;i++){
              var obj = {
                label:'',
                value:''
              }
              obj.label = patrolList[i].name;
              obj.value = patrolList[i].id;
              currentForm.selectOps.push(obj);
           }
        },response => {
          console.log(response);
        })
      }
  },
  mounted(){
      var vmForm = document.querySelector('.el-form.demo-dynamic');
      var form =  this.dynamicValidateForm.form;
      var vm = this;
      var n = 0;
      vmForm.addEventListener('DOMNodeInserted',function(e){
    
          if(e.target.parentNode.className ==="el-select right"){
               n++;
               console.log(n);
            var selectedBtn = document.querySelectorAll('.right.el-select');
            console.log(selectedBtn[(n-1)]);
            selectedBtn[(n-1)].addEventListener('click',function(){
               for(let i=0;i<selectedBtn.length;i++){
                 if(this ==selectedBtn[i]){
                   console.log(i);
                   console.log(form[i]);
                   var routeType = form[i].regionLeft;

                   vm.$http.get('http://localhost:8080/getPatrol?type='+routeType,{
    
                    }).then(response => {
                      console.log(response.data.patrolList);
                      let patrolList = response.data.patrolList;
                      for(let j = 0;j<patrolList.length;j++){
                          var obj = {
                            label:'',
                            value:''
                          }
                          obj.label = patrolList[j].name;
                          obj.value = patrolList[j].id;
                          form[i].selectOps.push(obj);
                      }
                    },response => {
                      console.log(response);
                    })
                 }
               }
            });
            // console.log(selectedBtn);
 
       
          }
      });

      function myClick(Btn,index){
        console.log(index)
      }
  }
}
</script>
<style>
   .custom-width .el-form-item__content .el-input{
      min-width:160px;
      margin-right:14px;
   } 
</style>