<template>
    <div>
        <Header></Header>
        <div class="m-blog">
            <h2>{{blog.title}}</h2>
            <el-link icon="el-icon-edit" v-if="ownBlog">
                <router-link :to="{name:'BlogEdit',params: {blogId: blog.id}}">
                    编辑
                </router-link>
            </el-link>
            <el-link type="danger" v-on:click="delblog" style="margin: 20px" v-if="ownBlog">删除</el-link>
            <el-divider></el-divider>
            <div class="markdown-body" v-html="blog.content"></div>
        </div>

    </div>
</template>

<script>

    import Header from "@/components/Header";
    import  "github-markdown-css/github-markdown.css"

    export default {
        name: "BlogDetail",
        components: {Header},
        data(){
            return {
                blog:{
                    id:"",
                    title :"",
                    content:" "
                },
                ownBlog: false

            }
        },
        methods:{
            delblog(){
                const blogId = this.$route.params.blogId;
                const _this = this
                if(blogId){
                    this.$confirm('此操作将永久删除该文章, 是否继续?', '提示', {
                        confirmButtonText: '确定',
                        cancelButtonText: '取消',
                        type: 'warning'
                    }).then(() => {
                        _this.$axios.post('/blogdel/'+blogId,null,{
                            headers:{
                                "Authorization": localStorage.getItem("token")
                            }
                        }).then(res=>{
                            this.$message({
                                type: 'success',
                                message: res.data.data
                            });
                            _this.$router.push('/blogs')
                        })

                    }).catch(() => {
                        this.$message({
                            type: 'info',
                            message: '已取消删除'
                        });
                    });
                }
            }
        },

        created() {
            const blogId = this.$route.params.blogId
            console.log(blogId)
            const _this = this
            _this.$axios.get('/blog/'+blogId).then(res =>{
                const mblog = res.data.data
                _this.blog.id =mblog.id
                _this.blog.title =mblog.title
                // 这个是markdown格式化文本格式的
                var MardownIt = require("markdown-it")
                var md = new MardownIt()
                var result = md.render(mblog.content)

                _this.blog.content =result

                _this.ownBlog = (mblog.userId ===_this.$store.getters.getUser.id)

            }) 
        }
    }
</script>

<style scoped>
    .m-blog{
        box-shadow: 0 2px 12px 0 rgba(0,0,0,0.1);
        width: 100%;
        min-height: 700px;
        padding: 20px 15px;
    }

</style>