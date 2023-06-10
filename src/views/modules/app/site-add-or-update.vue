<template>
  <el-dialog
    :title="!dataForm.sid ? '新增' : '修改'"
    :close-on-click-modal="false"
    :visible.sync="visible">
    <el-form :model="dataForm" :rules="dataRule" ref="dataForm" @keyup.enter.native="dataFormSubmit()" label-width="80px">
    <el-form-item label="场地名" prop="name">
      <el-input v-model="dataForm.name" placeholder="场地名"></el-input>
    </el-form-item>
    <el-form-item label="价格" prop="price">
      <el-input v-model="dataForm.price" placeholder="价格"></el-input>
    </el-form-item>
    <el-form-item label="楼层" prop="floor">
      <el-input v-model="dataForm.floor" placeholder="楼层"></el-input>
    </el-form-item>
    <el-form-item label="面积" prop="area">
      <el-input v-model="dataForm.area" placeholder="面积"></el-input>
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
          sid: 0,
          name: '',
          price: '',
          floor: '',
          area: ''
        },
        dataRule: {
          name: [
            { required: true, message: '场地名不能为空', trigger: 'blur' }
          ],
          price: [
            { required: true, message: '价格不能为空', trigger: 'blur' }
          ],
          floor: [
            { required: true, message: '楼层不能为空', trigger: 'blur' }
          ],
          area: [
            { required: true, message: '面积不能为空', trigger: 'blur' }
          ]
        }
      }
    },
    methods: {
      init (id) {
        this.dataForm.sid = id || 0
        this.visible = true
        this.$nextTick(() => {
          this.$refs['dataForm'].resetFields()
          if (this.dataForm.sid) {
            this.$http({
              url: this.$http.adornUrl(`/app/site/info/${this.dataForm.sid}`),
              method: 'get',
              params: this.$http.adornParams()
            }).then(({data}) => {
              if (data && data.code === 0) {
                this.dataForm.name = data.site.name
                this.dataForm.price = data.site.price
                this.dataForm.floor = data.site.floor
                this.dataForm.area = data.site.area
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
              url: this.$http.adornUrl(`/app/site/${!this.dataForm.sid ? 'save' : 'update'}`),
              method: 'post',
              data: this.$http.adornData({
                'sid': this.dataForm.sid || undefined,
                'name': this.dataForm.name,
                'price': this.dataForm.price,
                'floor': this.dataForm.floor,
                'area': this.dataForm.area
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
