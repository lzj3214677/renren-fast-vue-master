<template>
  <el-dialog
    :title="!dataForm.uid ? '新增' : '修改'"
    :close-on-click-modal="false"
    :visible.sync="visible">
    <el-form :model="dataForm" :rules="dataRule" ref="dataForm" @keyup.enter.native="dataFormSubmit()" label-width="80px">
    <el-form-item label="会员等级" prop="role">
      <el-select  v-model="dataForm.role" placeholder="角色" style="width: 100%"  >
        <el-option
          v-for="(item,index) of roleList"
          :key="index"
          :label="item.label"
          :value="item.value"
        >{{item.label}}</el-option>
      </el-select>
    </el-form-item>
    <el-form-item label="账号" prop="username">
      <el-input v-model="dataForm.username" placeholder="账号"></el-input>
    </el-form-item>
    <el-form-item label="密码" prop="password">
      <el-input v-model="dataForm.password" placeholder="密码"></el-input>
    </el-form-item>
    <el-form-item label="真实姓名" prop="realname">
      <el-input v-model="dataForm.realname" placeholder="真实姓名"></el-input>
    </el-form-item>
      <el-form-item label="电话" prop="mobile">
        <el-input v-model="dataForm.mobile" placeholder="电话"></el-input>
      </el-form-item>
    <el-form-item label="余额" prop="money">
      <el-input v-model="dataForm.money" placeholder="余额"></el-input>
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
          uid: 0,
          role: '',
          username: '',
          password: '',
          realname: '',
          money: '',
          mobile: ''
        },
        dataRule: {
          role: [
            { required: true, message: '角色不能为空', trigger: 'blur' }
          ],
          username: [
            { required: true, message: '账号不能为空', trigger: 'blur' }
          ],
          password: [
            { required: true, message: '密码不能为空', trigger: 'blur' }
          ],
          realname: [
            { required: true, message: '真实姓名不能为空', trigger: 'blur' }
          ],
          money: [
            { required: true, message: '余额不能为空', trigger: 'blur' }
          ],
          mobile: [
            { required: true, message: '电话不能为空', trigger: 'blue' }
          ]
        },
        roleList: [
          {
            label: '普通会员',
            value: '0'
          },
          {
            label: '白银会员',
            value: '1'
          },
          {
            label: '黄金会员',
            value: '2'
          }
        ],
      }
    },
    methods: {
      init (id) {
        this.dataForm.uid = id || 0
        this.visible = true
        this.$nextTick(() => {
          this.$refs['dataForm'].resetFields()
          if (this.dataForm.uid) {
            this.$http({
              url: this.$http.adornUrl(`/app/user/info/${this.dataForm.uid}`),
              method: 'get',
              params: this.$http.adornParams()
            }).then(({data}) => {
              if (data && data.code === 0) {
                this.dataForm.role = data.user.role
                this.dataForm.username = data.user.username
                this.dataForm.password = data.user.password
                this.dataForm.realname = data.user.realname
                this.dataForm.money = data.user.money
                this.dataForm.mobile = data.user.mobile
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
              url: this.$http.adornUrl(`/app/user/${!this.dataForm.uid ? 'save' : 'update'}`),
              method: 'post',
              data: this.$http.adornData({
                'uid': this.dataForm.uid || undefined,
                'role': this.dataForm.role,
                'username': this.dataForm.username,
                'password': this.dataForm.password,
                'realname': this.dataForm.realname,
                'money': this.dataForm.money,
                'mobile': this.dataForm.mobile
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
