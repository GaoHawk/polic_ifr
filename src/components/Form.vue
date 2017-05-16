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
      :label="'巡逻路线' + index"
      :key="form.key"
      :prop="'form.'+index+'.regionRight'"
      :rules="{
        required: true, message: '巡逻点不能为空', trigger: 'blur'
      }"
      :label-position="right"
    >
    <el-select 
    style="width:51%;"
    v-model="dynamicValidateForm.form[index].regionRight" 
    placeholder="请选择活动区域2">
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
              selectOps:[{
                  label:'区域一',
                  value:'shanghai'
              },{
                  label:'区域二',
                  value:'beijing'
              },{
                  label:'区域三',
                  value:'wuhan'
              }],
           }]
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
             regionRigth:'',
             selectOps:[]
        })
      },
      changePat(index){
        console.log(this.dynamicValidateForm.form[index]);
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