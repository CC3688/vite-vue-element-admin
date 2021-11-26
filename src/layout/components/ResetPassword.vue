<template>
  <el-dialog
    append-to-body
    v-bind="$attrs"
    :close-on-click-modal="false"
    v-on="$listeners"
  >
    <el-form ref="form" :model="formData" :rules="rules" label-width="80px" class="app-form" autocomplete="off">
      <el-form-item label="账号" prop="emplId">
        <el-input v-model="emplId" autocomplete="new-password" disabled />
      </el-form-item>
      <el-form-item label="密码" prop="password">
        <el-input v-model="formData.password" type="password" placeholder="需包含大小写字母和数字" autocomplete="new-password" />
      </el-form-item>
      <el-form-item label="确认密码" prop="password2">
        <el-input v-model="formData.password2" type="password" placeholder="需包含大小写字母和数字" autocomplete="new-password" />
      </el-form-item>
    </el-form>
    <el-row>
      <el-col class="p-t t-c">
        <el-button @click="cancle">取消</el-button>
        <el-button type="primary" @click="confirm">确认</el-button>
      </el-col>
    </el-row>
  </el-dialog>
</template>

<script>
import { AppCode } from '@/utils/constants'
import { mapState } from 'vuex'
import { passwordReset } from '@/api/user'

const rules = (that) => {
  return {
    password: [
      { required: true, message: '请输入密码', trigger: 'change' },
      { validator: (rule, value, callback) => {
        if (value && value.length < 6) {
          callback(new Error('密码不少于6位数'))
        } else {
          callback()
        }
      }, trigger: 'blur' },
      { validator: (rule, value, callback) => {
        if (/[a-z]/.test(value) && /[A-Z]/.test(value) && /[0-9]/.test(value)) {
          callback()
        } else {
          callback(new Error('需包含大小写字母和数字'))
        }
      }, trigger: 'blur' }
    ],
    password2: [
      { required: true, message: '请输入确认密码', trigger: 'change' },
      { validator: (rule, value, callback) => {
        if (that.formData.password !== that.formData.password2) {
          callback(new Error('两次密码不一致'))
        } else {
          callback()
        }
      }, trigger: 'blur' }
    ]
  }
}

export default {
  name: 'ResstPassword',
  data() {
    return {
      rules: rules(this),
      formData: {
        emplId: '',
        password: ''
      }
    }
  },
  computed: {
    ...mapState({
      emplId: state => state.user.emplId
    })
  },
  methods: {
    cancle() {
      this.$emit('update:visible', false)
    },
    confirm() {
      this.$refs['form'].validate(async(valid) => {
        if (valid) {
          const data = {
            appCode: AppCode,
            emplId: this.emplId,
            password: this.formData.password
          }
          await passwordReset(data)
          this.$message({
            type: 'success',
            message: '重置密码成功'
          })
          this.cancle()
          this.$refs['form'].resetFields()
          await this.$store.dispatch('user/logout')
          this.$router.push(`/login?redirect=${encodeURIComponent(this.$route.fullPath)}`)
        } else {
          console.log('error submit!!')
          return false
        }
      })
    }
  }
}
</script>

<style lang="scss" scoped>
.my-dialog {

}
</style>
<style lang="scss">
.my-dialog.el-dialog {
    top: 50%;
    left: 50%;
    transform: translate(-50%, -50%);
  .el-dialog__header {
    display: none;
  }
  width: 100vw;
  min-height: 100vh;
  background: transparent;
  box-shadow: none;
  margin: 0;
}
</style>
