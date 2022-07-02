<template>
    <div id =header>
        <h3>欢迎来到Bindada博客</h3>
        <el-avatar :size="50" :src="user.avatar"> </el-avatar>
        <div>{{user.username}}</div>
        <div class="maction">
            <span> <el-link  href="/blogs">主页</el-link></span>
            <el-divider direction="vertical"></el-divider>
            <span><el-link type="success" href="/blog/add">发布博客</el-link></span>
            <el-divider direction="vertical"></el-divider>
            <span v-show="!hasLogin">
              <el-link type="primary" @click="login">登录</el-link>
            </span>
            <span v-show="hasLogin">
              <el-link type="danger" @click="logout">退出</el-link>
            </span>
        </div>
    </div>
</template>

<script>
    export default {
        name: "Header",
        data(){
            return{
                user:{
                    username:"请先登录",
                    avatar:"https://cube.elemecdn.com/9/c2/f0ee8a3c7c9638a54940382568c9dpng.png"
                },
                hasLogin: false
            }
        },
        methods:{
            //退出方法
            logout(){
                const _this= this
                _this.$axios.get("http://localhost:8081/logout",{
                    headers:{
                        "Authorization": localStorage.getItem("token")
                    }
                }).then(res =>{
                    _this.$store.commit("REMOVE_INFO")
                    _this.$router.push("/login")
                })
            },
            login(){
                this.$router.push("/login")
            }
        },
        created () {
            console.log("=======123==")
            console.log(this.$store.getters.getUser)
            if (this.$store.getters.getUser.username) {//如果username不为空
                this.user.username = this.$store.getters.getUser.username
                this.user.avatar = this.$store.getters.getUser.avatar
                //判断是登录状态还是非登录显示 退出按钮或者登录按钮
                this.hasLogin = true;
            }
        }
    }
</script>

<style scoped>
    #header{
        max-width: 1200px;
        text-align: center;
        margin: 0 auto;
    }
    .maction{
        margin: 10px;
    }
</style>