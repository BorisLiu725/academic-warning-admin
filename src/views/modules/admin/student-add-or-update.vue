<template>
  <el-dialog
    :title="!dataForm.id ? '新增' : '修改'"
    :close-on-click-modal="false"
    :visible.sync="visible">
    <el-form :model="dataForm" :rules="dataRule" ref="dataForm" @keyup.enter.native="dataFormSubmit()" label-width="80px">
    <el-form-item label="姓名" prop="name">
      <el-input v-model="dataForm.name" placeholder="姓名"></el-input>
    </el-form-item>
    <el-form-item label="性别 1-男 2-女" prop="sex">
      <el-input v-model="dataForm.sex" placeholder="性别 1-男 2-女"></el-input>
    </el-form-item>
    <el-form-item label="年龄" prop="age">
      <el-input v-model="dataForm.age" placeholder="年龄"></el-input>
    </el-form-item>
    <el-form-item label="手机号" prop="phone">
      <el-input v-model="dataForm.phone" placeholder="手机号"></el-input>
    </el-form-item>
    <el-form-item label="1-专科 2-本科 3-研究生 4-博士" prop="education">
      <el-input v-model="dataForm.education" placeholder="1-专科 2-本科 3-研究生 4-博士"></el-input>
    </el-form-item>
    <el-form-item label="学校代码" prop="schoolId">
      <el-input v-model="dataForm.schoolId" placeholder="学校代码"></el-input>
    </el-form-item>
    <el-form-item label="班级id" prop="classId">
      <el-input v-model="dataForm.classId" placeholder="班级id"></el-input>
    </el-form-item>
    <el-form-item label="专业id" prop="majorId">
      <el-input v-model="dataForm.majorId" placeholder="专业id"></el-input>
    </el-form-item>
    <el-form-item label="创建时间" prop="createTime">
      <el-input v-model="dataForm.createTime" placeholder="创建时间"></el-input>
    </el-form-item>
    <el-form-item label="更新时间" prop="updateTime">
      <el-input v-model="dataForm.updateTime" placeholder="更新时间"></el-input>
    </el-form-item>
    </el-form>
    <span slot="footer" class="dialog-footer">
      <el-button @click="visible = false">取消</el-button>
      <el-button type="primary" @click="dataFormSubmit()">确定</el-button>
    </span>
  </el-dialog>
</template>

<script>
  export default {
    data () {
      return {
        visible: false,
        dataForm: {
          id: 0,
          name: '',
          sex: '',
          age: '',
          phone: '',
          education: '',
          schoolId: '',
          classId: '',
          majorId: '',
          createTime: '',
          updateTime: ''
        },
        dataRule: {
          name: [
            { required: true, message: '姓名不能为空', trigger: 'blur' }
          ],
          sex: [
            { required: true, message: '性别 1-男 2-女不能为空', trigger: 'blur' }
          ],
          age: [
            { required: true, message: '年龄不能为空', trigger: 'blur' }
          ],
          phone: [
            { required: true, message: '手机号不能为空', trigger: 'blur' }
          ],
          education: [
            { required: true, message: '1-专科 2-本科 3-研究生 4-博士不能为空', trigger: 'blur' }
          ],
          schoolId: [
            { required: true, message: '学校代码不能为空', trigger: 'blur' }
          ],
          classId: [
            { required: true, message: '班级id不能为空', trigger: 'blur' }
          ],
          majorId: [
            { required: true, message: '专业id不能为空', trigger: 'blur' }
          ],
          createTime: [
            { required: true, message: '创建时间不能为空', trigger: 'blur' }
          ],
          updateTime: [
            { required: true, message: '更新时间不能为空', trigger: 'blur' }
          ]
        }
      }
    },
    methods: {
      init (id) {
        this.dataForm.id = id || 0
        this.visible = true
        this.$nextTick(() => {
          this.$refs['dataForm'].resetFields()
          if (this.dataForm.id) {
            this.$http({
              url: this.$http.adornUrl(`/admin/student/info/${this.dataForm.id}`),
              method: 'get',
              params: this.$http.adornParams()
            }).then(({data}) => {
              if (data && data.code === 0) {
                this.dataForm.name = data.student.name
                this.dataForm.sex = data.student.sex
                this.dataForm.age = data.student.age
                this.dataForm.phone = data.student.phone
                this.dataForm.education = data.student.education
                this.dataForm.schoolId = data.student.schoolId
                this.dataForm.classId = data.student.classId
                this.dataForm.majorId = data.student.majorId
                this.dataForm.createTime = data.student.createTime
                this.dataForm.updateTime = data.student.updateTime
              }
            })
          }
        })
      },
      // 表单提交
      dataFormSubmit () {
        this.$refs['dataForm'].validate((valid) => {
          if (valid) {
            this.$http({
              url: this.$http.adornUrl(`/admin/student/${!this.dataForm.id ? 'save' : 'update'}`),
              method: 'post',
              data: this.$http.adornData({
                'id': this.dataForm.id || undefined,
                'name': this.dataForm.name,
                'sex': this.dataForm.sex,
                'age': this.dataForm.age,
                'phone': this.dataForm.phone,
                'education': this.dataForm.education,
                'schoolId': this.dataForm.schoolId,
                'classId': this.dataForm.classId,
                'majorId': this.dataForm.majorId,
                'createTime': this.dataForm.createTime,
                'updateTime': this.dataForm.updateTime
              })
            }).then(({data}) => {
              if (data && data.code === 0) {
                this.$message({
                  message: '操作成功',
                  type: 'success',
                  duration: 1500,
                  onClose: () => {
                    this.visible = false
                    this.$emit('refreshDataList')
                  }
                })
              } else {
                this.$message.error(data.msg)
              }
            })
          }
        })
      }
    }
  }
</script>
