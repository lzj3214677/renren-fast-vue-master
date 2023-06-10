<template>
  <el-dialog
    :title="!dataForm.rid ? '新增' : '修改'"
    :close-on-click-modal="false"
    :visible.sync="visible">
    <el-form :model="dataForm" :rules="dataRule" ref="dataForm" @keyup.enter.native="dataFormSubmit()" label-width="80px">
    <el-form-item label="用户等级" prop="userlevel">
      <el-select  v-model="dataForm.userlevel" placeholder="用户等级" style="width: 100%"  >
        <el-option
          v-for="(item,index) of roleList"
          :key="index"
          :label="item.label"
          :value="item.value"
        >{{item.label}}</el-option>
      </el-select>
    </el-form-item>
    <el-form-item label="开始时间" prop="starttime">
      <el-date-picker style="width: 100%"
                      placeholder="开始时间"
                      value-format="yyyy-MM-dd 00:00:00"
                      v-model="dataForm.starttime"
      ></el-date-picker>
    </el-form-item>
    <el-form-item label="结束时间" prop="endtime">
      <el-date-picker style="width: 100%"
                      placeholder="结束时间"
                      value-format="yyyy-MM-dd 23:59:59"
                      v-model="dataForm.endtime"
      ></el-date-picker>
    </el-form-item>
      <el-form-item label="场地" prop="price">
        <el-select  v-model="dataForm.sid" placeholder="场地" style="width: 100%"  @click.native="getSiteList()" >
          <el-option
            v-for="(item,index) of siteList"
            :key="index"
            :label="item.name"
            :value="item.sid"
          >{{item.name}}</el-option>
        </el-select>
      </el-form-item>
    <el-form-item label="价格" prop="price">
      <el-input v-model="dataForm.price" placeholder="价格"></el-input>
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
          rid: 0,
          userlevel: '',
          starttime: '',
          endtime: '',
          sid: '',
          price: ''
        },
        dataRule: {
          userlevel: [
            { required: true, message: '用户等级不能为空', trigger: 'blur' }
          ],
          starttime: [
            { required: true, message: '开始时间不能为空', trigger: 'blur' }
          ],
          endtime: [
            { required: true, message: '结束时间不能为空', trigger: 'blur' }
          ],
          price: [
            { required: true, message: '价格不能为空', trigger: 'blur' }
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
        siteList: []
      }
    },
    methods: {
      init (id) {
        this.dataForm.rid = id || 0
        this.visible = true
        this.$nextTick(() => {
          this.$refs['dataForm'].resetFields()
          if (this.dataForm.rid) {
            this.$http({
              url: this.$http.adornUrl(`/app/rule/info/${this.dataForm.rid}`),
              method: 'get',
              params: this.$http.adornParams()
            }).then(({data}) => {
              if (data && data.code === 0) {
                this.dataForm.userlevel = data.rule.userlevel
                this.dataForm.starttime = data.rule.starttime
                this.dataForm.endtime = data.rule.endtime
                this.dataForm.sid = data.rule.sid
                this.dataForm.price = data.rule.price
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
              url: this.$http.adornUrl(`/app/rule/${!this.dataForm.rid ? 'save' : 'update'}`),
              method: 'post',
              data: this.$http.adornData({
                'rid': this.dataForm.rid || undefined,
                'userlevel': this.dataForm.userlevel,
                'starttime': this.dataForm.starttime,
                'endtime': this.dataForm.endtime,
                'sid': this.dataForm.sid,
                'price': this.dataForm.price
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
      },
      getSiteList () {
        this.$http({
          url: this.$http.adornUrl('/app/site/list'),
          method: 'get',
        }).then(({data}) => {
          this.siteList = data.page.list
        })
      }
    }
  }
</script>
