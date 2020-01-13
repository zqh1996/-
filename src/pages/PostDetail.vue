<template>
  <div class="container">
    <div class="article" v-if="detail.type === 1">

        <div class="header">
            <!-- $router.back()返回去上一页，判断是否有上一页 -->
            <div class="header-left" @click="$router.back()">
                <span class="iconfont iconjiantou2"></span>
                <span class="iconfont iconnew"></span>
            </div>
            <span class="focus" v-if="!detail.has_follow" @click="handleFollow">关注</span>
            <span class="focus focus_active" v-else @click="handleUnfollow">已关注</span>
        </div>

        <h3>{{ detail.title }}</h3>

        <p class="post-info">{{ detail.user.nickname }}  2019-10-10</p>

        <div class="post-content" v-html="detail.content"></div>
    </div>
   <!-- 按钮列表 -->
    <div class="post-btns">
        <!-- 点赞 -->
        <span 
        @click="handleLike" 
        :class="{ like_active: detail.has_like }
        ">
            <i class="iconfont icondianzan"></i>
            {{detail.like_length}}
        </span>
       
        <span>
            <i class="iconfont iconweixin"></i>
            <a href="https://developers.weixin.qq.com/doc/offiaccount/OA_Web_Apps/JS-SDK.html#1">微信</a>
        </span>
        
    </div>
  </div>
</template>

<script>
export default{
    data(){
        return {
            // 文章的详情
            detail:{
                // user需要在模板中渲染，不然页面会报错
                user:{}
            }
        }
    },
    methods:{
        // 关注当前的作者
        handleFollow(){
            // 通过作者id关注用户
            this.$axios({
                url: "/user_follows/" + this.detail.user.id,
                // 添加头信息
                headers: {
                    Authorization: localStorage.getItem("token")
                }
            }).then(res => {
                const {message} = res.data;

                if(message === "关注成功"){
                    // 修改关注的按钮的状态
                    this.detail.has_follow = true;
                    this.$toast.success(message)
                }
            })
        }, 
       // 取消关注
        handleUnfollow(){
            // 通过作者id关注用户
            this.$axios({
                url: "/user_unfollow/" + this.detail.user.id,
                // 添加头信息
                headers: {
                    Authorization: localStorage.getItem("token")
                }
            }).then(res => {
                const {message} = res.data;

                if(message === "取消关注成功"){
                    // 修改关注的按钮的状态
                    this.detail.has_follow = false;
                    this.$toast.success(message)
                }
            })
        },
        //点赞
        handleLike(){
           //通过文章点赞
           this.$axios({
                url: "/post_like/" + this.detail.id,
                // 添加头信息
                headers: {
                    Authorization: localStorage.getItem("token")
                }
           }).then(res=>{
               const{message}=res.data;
               if(message === "点赞成功"){
                   // 修改点赞的按钮的状态
                   this.detail.has_like=true;
                   //向服务器修改点赞总数
                   this.detail.like_length++;
               }
                if(message === "取消成功"){
                    // 修改点赞的按钮的状态
                    this.detail.has_like = false;
                    //向服务器修改点赞总数
                    this.detail.like_length--;
                }
                this.$toast.success(message);
           })
        }

    },
    mounted(){
        // 请求文章的详情
        const {id} = this.$route.params;
        //token
        const token=localStorage.getItem("token");
        // 请求配置
        const config={
             url: "/post/" + id
        }
        // 如果有token就带上，才可以获取到是否关注，是否收藏属性
        if(token){
            config.headers={
                Authorization:token
            }
        }
        this.$axios(config).then(res=>{
            const {data} = res.data;
            // 保存到详情
            this.detail=data;
        })
    }
}
</script>

<style lang="less" scoped>
.container{
    padding-bottom: 100 / 360 * 100vw;
}
.video{
    width:100%;
}
.video-info{
    padding:10px;
    display:flex;
    justify-content: space-between;
    align-items: center;

    .video-user{
        display: flex;
        align-items: center;
        font-size:12px;
        color:#999;
    }
    img{
        width:18px;
        height: 18px;
        border-radius: 50%;
        margin-right: 10px;
    }
}
.focus{
    width: 62 / 360 * 100vw;
    height:26 / 360 * 100vw;
    line-height: 26 / 360 * 100vw;
    text-align:center;
    font-size:12px;
    background: red;
    color:#fff;
    border-radius: 100px;
    border:1px red solid;
}
.focus_active{
    border:1px #ccc solid;
    color:#333;
    background: none;
}
.article{
    padding:0 10px;
    padding-top: 60 / 360 * 100vw;
    .header{
        position: fixed;
        top:0;
        left:0;
        width:100%;
        height:60 / 360 * 100vw;
        padding:0 10px;
        box-sizing:border-box;
        display: flex;
        justify-content: space-between;
        align-items: center;
        background: #fff;
        
        .header-left{
            *{
                vertical-align: middle;
            }
        }

        .iconnew{
            font-size:50px;
        }
    }
    h3{
        margin-bottom: 5px;
    }
    .post-info{
        font-size: 12px;
        color:#999;
        margin-bottom: 10px;
    }
    .post-content{
        line-height: 1.5;
        /deep/img{
            max-width: 100%;
        }
    }
}
.post-btns{
    margin-top: 30px;
    display: flex;
    justify-content: space-around;
    span{
        padding:0 15px;
        height:30 / 360 * 100vw;
        line-height: 30 / 360 * 100vw;
        font-size:12px;
        border:1px #ddd solid;
        border-radius: 50px;

        *{
            vertical-align: middle;
        }
    }
    .iconweixin{
        color: #07c907;
    }
    .like_active{
        border:1px red solid;
        i{
            color:red;
        }
    }
}
</style>