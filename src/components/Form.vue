<template>
 <el-form :model="dynamicValidateForm" ref="dynamicValidateForm" label-width="90px" class="demo-dynamic" style="width:600px;">
  <el-form-item
    prop="polic"
    label="警种"
    :rules="[
      { required: true, message: '请选择警种', trigger: 'change' },
    ]"
  >
  <el-select v-model="dynamicValidateForm.polic" placeholder="请选择警种"
    style="float:left;width:34%;"
    >
    <el-option label="刑侦民警" value="0"></el-option>
    <el-option label="社区民警" value="1"></el-option>
    <el-option label="治安民警" value="2"></el-option>
    <el-option label="巡逻民警" value="3"></el-option>
    <el-option label="特警" value="4"></el-option>
    <el-option label="辅警" value="5"></el-option>
    <el-option label="交警" value="6"></el-option>
  </el-select>
  </el-form-item>

  <el-form-item label="姓名" prop="name"
    :rules="[
      { required:true, message:'姓名不能为空', trigger:'blur' },

      { min:2,max:8,message:'姓名长度为 2到8个字符',trigger:'blur,change'}
    ]"
  >
    <el-input v-model="dynamicValidateForm.name"></el-input>
  </el-form-item>

  <el-form-item label="联系方式" prop="phone"
    :rules="[
      { required:true, message:'联系方式不能为空', trigger:'blur' },
      { max:11, message:'请输入正确的电话号码', trigger:'blur,change'}
    ]"
  >
    <el-input v-model="dynamicValidateForm.phone"></el-input>
  </el-form-item>

  <el-form-item label="编号" prop="number"
    :rules="[
      { required: true, message: '编号不能为空', trigger: 'blur' },
      { min:6, max:8, message: '警员编号6到8个字符', trigger: 'blur,change' }
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
      required: true, message: '巡逻路线不能为空', trigger: 'change'
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
    <el-button @click="addDomain">新增巡逻路线</el-button>
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
        if(userId){
          this.$http.get('/getUserInfo',{
            params:{
              userid:Number(userId)
            }
          }).then(response => {
            console.log(response.data);
            this.dynamicValidateForm.polic = (response.data.usertype).toString();
            this.dynamicValidateForm.name = response.data.userName;
            this.dynamicValidateForm.phone = response.data.phone;
            this.dynamicValidateForm.number = response.data.policeNumber;
            let patroStr = response.data.patrolids;
            if(patroStr && patroStr.length>0){
                let patroArr = patroStr.substr(1,patroStr.length-1).split(',');
                if(patroArr[patroArr.length-1] == ""){
                  patroArr.length = patroArr.length - 1;
                }
                console.log(patroArr);
                let patroNames = response.data.patrolnames;
                let pNameArr = patroNames.substr(1,patroNames.length-1).split(',');
                if(pNameArr[pNameArr.length-1] == ""){
                  pNameArr.length = pNameArr.length - 1;
                }
                console.log(pNameArr);
                let patroTypes = response.data.patrolids_type;
                let TypeArr = patroTypes.substr(1,patroTypes.length-1).split(',');
                if(TypeArr[TypeArr.length-1] == ""){
                  TypeArr.length = TypeArr.length - 1;
                }
                console.log(TypeArr);
                this.dynamicValidateForm.form = [];
                for(let i = 0;i<patroArr.length;i++){
                    var newStr = patroArr[i];
                    this.dynamicValidateForm.form.push({
                        regionLeft:TypeArr[i],
                        key: Date.now(),
                        regionRight:patroArr[i],
                        selectOps:[{
                           label:pNameArr[i],
                           value:patroArr[i]
                        }]
                    })
                }
            }
            // console.log(this.dynamicValidateForm.form);
            var selectedBtn = document.querySelectorAll('.right.el-select');

          },response => {
            console.log(response);
          })
        }

  },
  name: 'hello',
  data () {
    return {
       dynamicValidateForm:{
           polic:'',
           name:'',
           phone:'',
           number:'',
           form:[{
             regionLeft:'',
             regionRight:'',
             selectOps:[]
           }]
       },
       policeOps:['刑侦民警','社区民警','治安民警','巡逻民警','特警','辅警','交警']
    };
  },
  methods:{
     submitForm(formName) {
        this.$refs[formName].validate((valid) => {
          if (valid) {
            var patrolids = '';
            var patrolnames = '';
            var checkSubm = true;
            var uid = window.location.search;
             console.log(uid);
            var userId = uid.split('=')[1];
            var patroTotal = [];

            for(let i = 0;i<this.dynamicValidateForm.form.length;i++){
              let form = this.dynamicValidateForm.form[i];
              if(form.regionRight.length==0){
                var rSelect = document.querySelectorAll('.el-select.right');
                var errDiv = document.createElement("div");
                errDiv.setAttribute('class','el-form-item__error');
                errDiv.innerHTML = `巡逻点不能为空`;
                `<div class="el-form-item__error">巡逻点不能为空</div>`;
                rSelect[i].appendChild(errDiv);
                console.log('error sub');
                checkSubm = false;
                // return false;
              }
              console.log(form.regionRight);
              patrolids += ","+ form.regionRight;

              for(let j =0;j<form.selectOps.length;j++){
                patroTotal.push(form.selectOps[j]);
                // if(form.selectOps[j].value == form.regionRight){
                //    patrolnames += ","+form.selectOps[j].label;
                // }
              }
            }

            let patroArr = patrolids.substr(1,patrolids.length-1).split(',');
            console.log(patroArr);
            for(let i = 0;i<patroArr.length;i++){
              for(let j =0;j<patroTotal.length;j++){
                 console.log(patroTotal[j].value,Number(patroArr[i]))
                  if(patroTotal[j].value == Number(patroArr[i])){
 
                    patrolnames += ","+ patroTotal[j].label;
                  }
              }
            }
            if(!checkSubm){
                return false;
            }

            this.$http({
                method: 'post',
                url:'/modifyUserInfo',
                data:{
                    usertype:this.dynamicValidateForm.polic,
                    nickname:this.dynamicValidateForm.name,
                    phone:this.dynamicValidateForm.phone,
                    police_number:this.dynamicValidateForm.number,
                    patrolids:patrolids,
                    patrolnames:patrolnames,
                    id: userId?userId:0
                }

            }).then(response => {
                console.log(response.data);
                let uid = response.data.id;
                // var classData = response.data.data;
                // for(let i=0;i<classData.length;i++){
                //   let classObj = {
                //       classID:classData[i].id,
                //       className:classData[i].grade + classData[i].name
                //   }
                //   this.$store.commit('SET_HAND_CLASS',classObj);
                // }
               var ifr = window.parent.document.getElementById("mainIframe");
               window.parent.document.getElementById("mainIframe").style.display = "none";
               ifr.nextElementSibling.style.display = 'none';
               // updateRow 修改数据
              var thisRow = window.parent.document.getElementById(userId);
              if(thisRow){
                  var childTd = thisRow.parentNode.parentNode.children;
                  childTd[1].innerHTML = this.dynamicValidateForm.name;
                  childTd[2].innerHTML = this.dynamicValidateForm.phone;
                  childTd[3].innerHTML = this.dynamicValidateForm.number;
                  childTd[4].innerHTML = patrolnames;
                  childTd[0].innerHTML = this.policeOps[this.dynamicValidateForm.polic];
              }else
              // 添加新数据 addRow
              {
                var list  = window.parent.document.querySelector('#tableForm tbody');
                console.log(list);
                // 创建新节点添加到页面上
                var trEle = document.createElement('tr');
                var element = `
                  <td>${this.policeOps[this.dynamicValidateForm.polic]}</td>
                  <td>${this.dynamicValidateForm.name}</td>
                  <td>${this.dynamicValidateForm.phone}</td>
                  <td>${this.dynamicValidateForm.number}</td>
                  <td>${patrolnames}</td>
                  <td><a style="text-align:center;color:blue;cursor:pointer;" name="${patrolnames}" date-id="${patrolids}" onclick="updateRow(this,${uid});" id="${uid}">修改</a> <a style="text-align:center;color:blue;cursor:pointer;" onclick="delRow(this,${uid});">删除</a></td>
                `;
                trEle.innerHTML = element;
                list.insertBefore(trEle,list.children[1]);
              }
              //  ifr.parentNode.parentNode.parentNode.location.reload();
              //  window.parent.reload();
            }, response => {

                console.log(response)
            })

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
        var selectedBtn = document.querySelectorAll('.right.el-select');
        var errNode = selectedBtn[index].querySelector('.el-form-item__error');
        console.log(errNode);
        if(errNode){
            selectedBtn[index].removeChild(errNode);
       }
       console.log(selectedBtn[index]);
       console.log(patralP);
      },
      changePat(index){
        var routeType= this.dynamicValidateForm.form[index].regionLeft;
        console.log(routeType);
        var currentForm = this.dynamicValidateForm.form[index];
        currentForm.selectOps = [];
        currentForm.regionRight = '';
        this.$http.get('/getPatrol?type='+routeType,{
    
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
      var vm = this;
      var n = 0;
      var uid = window.location.search;
        console.log(uid);
      var userId = uid.split('=')[1]

      if(userId){
        vmForm.addEventListener('DOMNodeInserted',function(e){
          if(e.target.parentNode.className ==="el-select right"  ){
                  n++;
               console.log(n);
            var selectedBtn = document.querySelectorAll('.right.el-select');
            console.log(selectedBtn[(n-1)]);
            selectedBtn[(n-1)].addEventListener('click',function(){
              var form = vm.dynamicValidateForm.form;
               for(let i=0;i<selectedBtn.length;i++){
                 if(this ==selectedBtn[i]){
                   console.log(i);
                   console.log(form[i]);
                   var routeType = form[i].regionLeft;

                   vm.$http.get('/getPatrol?type='+routeType,{
    
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