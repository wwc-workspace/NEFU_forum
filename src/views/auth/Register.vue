<template xmlns:v-validator="http://www.w3.org/1999/xhtml">
  <div class="row">
    <div class="col-md-4 col-md-offset-4 floating-box">
    <Message :show.sync="msgShow" :type="msgType" :msg="msg"/>
      <div class="panel panel-default">
        <div class="panel-heading">
          <h3 class="panel-title">请注册</h3>
        </div>

        <div class="panel-body" data-validator-form>
          <div class="form-group">
            <label class="control-label">用户名</label>
            <input v-model.trim="username" v-validator:input.required="{ regex: /^[a-zA-Z]+\w*\s?\w*$/, error: '用户名要求以字母开头的单词字符' }" type="text" class="form-control" placeholder="请填写用户名">
          </div>
          <div class="form-group">
            <label class="control-label">昵称</label>
            <input v-model.trim="nickName" v-validator:input.required="{ error: '用户名不能为空' }" maxlength="10" minlength="3" type="text" class="form-control" placeholder="请填写用户名">
          </div>
          <div class="form-group">
            <label class="control-label">密码</label>
            <input id="password" v-model.trim="password" v-validator.required="{ regex: /^\w{6,16}$/, error: '密码要求 6 ~ 16 个单词字符' }" type="password" class="form-control" placeholder="请填写密码">
          </div>
          <div class="form-group">
            <label class="control-label">确认密码</label>
            <input v-model.trim="cpassword" v-validator.required="{ target: '#password' }" type="password" class="form-control" placeholder="请填写确认密码">
          </div>
          <div class="form-group">
            <label class="control-label">邮箱</label>
            <input v-model.trim="email" v-validator.required="{error:'请输入邮箱'}" type="email" class="form-control" placeholder="请填写邮箱"/>
          </div>
          <div class="form-group">
            <label class="control-label">图片验证码</label>
            <input v-model.trim="captcha" v-validator.required="{ title: '图片验证码' }" type="text" class="form-control" placeholder="请填写验证码">
          </div>
          <div class="thumbnail" title="点击图片重新获取验证码" @click="getCaptcha">
            <div class="captcha vcenter" v-html="captchaTpl"></div>
          </div>
          <button type="submit" class="btn btn-lg btn-success btn-block" @click="register">
            <i class="fa fa-btn fa-sign-in"></i> 注册
          </button>
        </div>
      </div>
    </div>
  </div>
</template>

<script>
import createCaptcha from '@/utils/createCaptcha'
import ls from '@/utils/localStorage'
import sessionStorage from "../../utils/sessionStorage";
import md5 from 'md5'


export default {
  name: 'Register',
  data() {
    return {
      captchaTpl: '', // 验证码模板
      username: '', // 用户名
      nickName:'',//昵称
      password: '', // 密码
      cpassword: '', // 确认密码
      email:'',//邮箱
      captcha: '', // 验证码
      msg: '', // 消息
      msgType: '', // 消息类型
      msgShow: false // 是否显示消息，默认不显示
    }
  },

  created() {
    this.getCaptcha()
  },
  methods: {
    //获取验证码图片
    getCaptcha() {
      const { tpl, captcha } = createCaptcha(6)

      this.captchaTpl = tpl
      this.localCaptcha = captcha
    },
    //验证
    register(e) {
      this.$nextTick(() => {
        const target = e.target.type === 'submit' ? e.target : e.target.parentElement

        if (target.canSubmit) {
          this.submit()
        }
      })
    },
    //注册
    submit() {
      if (this.captcha.toUpperCase() !== this.localCaptcha) {
        this.showMsg('验证码不正确')
        this.getCaptcha()
      } else {
        var user = {
          name:this.nickName,
          account: this.username,
          password:md5(this.password),
          //avatar: `https://api.adorable.io/avatars/200/${this.username}.png`,
          email:this.email
        };
        //向服务器返回数据
        this.$axios.post("http://10.42.0.118:8080/register",user)
          .then(response=>{
            if(response.status===201){
              this.login();
            } else this.showMsg("注册失败");
          })
          .catch(error=>{this.showMsg("注册失败")})
      }
    },
    //注册成功，直接登录
    login() {
      this.$router.push('/auth/login');
      this.showMsg('注册成功', 'success');
    },
    showMsg(msg, type = 'warning') {
      this.msg = msg;
      this.msgType = type;
      this.msgShow = false;

      this.$nextTick(() => {
        this.msgShow = true
      })
    }
  }
}
</script>

<style scoped>
.thumbnail { width: 170px; margin-top: 10px; cursor: pointer;}
.thumbnail .captcha { height: 46px; background: #E1E6E8;}
.captcha { font-size: 24px; font-weight: bold; user-select: none;}
</style>
