<template>
    <el-container>
        <el-header>Vueblog</el-header>
        <el-main>
            <el-form :model="ruleForm" :rules="rules" ref="ruleForm" label-width="100px" class="demo-ruleForm">
                <el-form-item label="用户名" prop="username">
                    <el-input v-model="ruleForm.username"></el-input>
                </el-form-item>
                <el-form-item label="密码" prop="password">
                    <el-input v-model="ruleForm.password"></el-input>
                </el-form-item>
                <el-form-item >
                    <el-button type="primary" class="btn1" @click="submitForm('ruleForm')">登录</el-button>
                    <el-button class="btn1" @click="resetForm('ruleForm')">重置</el-button>
                </el-form-item>
            </el-form>
        </el-main>
    </el-container>
</template>

<script>
    export default {
        name: "Login",
        data() {
            return {
                ruleForm: {
                    username: '',
                    password: '',
                },
                rules: {
                    username: [
                        { required: true, message: '请输入用户名', trigger: 'blur' },
                        { min: 3, max: 15, message: '长度在 3 到 15 个字符', trigger: 'blur' }
                    ],
                    password: [
                        { required: true, message: '请输入密码', trigger: 'change' }
                    ]
                }
            };
        },
        methods: {
            submitForm(formName) {
                this.$refs[formName].validate((valid) => {
                    if (valid) {
                        const _this = this;
                        console.log("校验成功");
                        _this.$axios.post('http://localhost:8081/login',_this.ruleForm).then(response =>{
                            const jwt =response.headers['authorization']
                            const userInfo = response.data.data;
                            console.log(userInfo);
                            _this.$store.commit("SET_TOKEN",jwt);
                            _this.$store.commit("SET_USERINFO",userInfo);
                            _this.$router.push("/blogs")
                        });
                    } else {
                        console.log('error submit!!');
                        return false;
                    }
                });
            },
            resetForm(formName) {
                this.$refs[formName].resetFields();
            }
        }
    }
</script>

<style scoped>
    .el-header{
        background-color: #B3C0D1;
        line-height: 60px;
        text-align: center;
    }

    .el-main {
        color: #333;
        text-align: center;
        line-height: 160px;
    }
    .btn1{
        margin: auto;
    }
    .demo-ruleForm{
        max-width: 500px;
        margin: 0 auto;
    }

</style>