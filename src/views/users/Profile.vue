<template xmlns:v-validator="http://www.w3.org/1999/xhtml">
  <div class="col-md-9 left-col">
    <div class="panel panel-default padding-md">
        <div class="panel-body">
            <h2><i class="fa fa-cog"></i> 编辑个人资料</h2>
        <hr>
        <div class="form-horizontal" data-validator-form>
          <div class="form-group">
            <label class="col-sm-2 control-label">昵称</label>
            <div class="col-sm-6">
              <input v-model.trim="nickName" v-validator:input.required="{ title: '昵称', error: '昵称不能为空' }" minlength="3" maxlength="16" type="text" class="form-control">
            </div>
          </div>
          <div class="form-group">
           <!--修改邮箱-->
            <label class="col-sm-2 control-label">邮箱</label>
            <div class="col-sm-6">
              <input v-model.trim="email" v-validator:input.required="{ title: '邮箱', error: '昵称不能为空' }"  type="email" class="form-control">
            </div>
          </div>
          <div class="form-group">
            <div class="col-sm-offset-2 col-sm-6">
              <button type="submit" class="btn btn-primary" @click="updateProfile">应用修改</button>
            </div>
          </div>
        </div>
        </div>
    </div>
  </div>
</template>

<script>
import sessionStorage from "../../utils/sessionStorage";

export default {
  name: 'EditProfile',
  data() {
    return {
      nickName:this.$store.state.user.name,//昵称
      email:this.$store.state.user.email//邮箱
    }
  },
    beforeCreate() {

  },
  methods: {
    updateProfile(e) {
      this.$nextTick(() => {
        if (e.target.canSubmit) {
            var stageUser=this.$store.state.user;
            var changeUser={
              nickName:this.nickName,
              email:this.email
            };
            changeUser={...stageUser,...changeUser};
             this.$axios.post('/',changeUser)
               .then(response=>{
                   if (response.status===200)
                   {
                      this.$store.dispatch('updateUser',changeUser);
                      this.showMsg("修改成功");
                   }
                   else this.showMsg("修改失败");
               })
               .catch(error=>{this.showMsg("修改失败")})
        }
      })
    }
  }
}
</script>

<style scoped>

</style>
