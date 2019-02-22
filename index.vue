<template>
    <div class="login">
        <div class="login_b"></div>
        <div class="login_c">
            <div class="log_tit">
                登录VIP蜂校
                <!-- <i class="i_close el-icon-close"></i> -->
            </div>
            <div class="log_con">
                <ul class="log_con_ul ul_pas" v-if="isLoginTab">
                    <li>
                        <p class="pt">
                            <input class="inp" :class="isPhoneTrue?'act':''" maxlength="11"  v-model="mPhoneNum" type="text" placeholder="请输入手机号" @input="addPhone"/>
                        </p>
                        <p class="pb">
                            <span v-if="isPhoneTrue" class="stex">{{mPhoneErrText}}</span>
                            <span v-if="isPhoneNot" class="stex">该手机号尚未注册，点击<a style="color:#0B6BC0;" href="javascript:;" @click="newRegister">立即注册</a></span>
                        </p>
                    </li>
                    <li>
                        <p class="pt">
                            <input class="inp" :class="isPassTrue?'act':''" type="text" maxlength="10" v-model="mPassNum" @input="checkPass" placeholder="请输入密码"/>
                        </p>
                        <p class="pb">
                            <span v-if="isPassTrue" class="stex">{{mPassErrText}}</span>
                            <span v-if="isPhoneOrPassTrue" class="stex">{{mPhoneOrPassErrText}}</span>
                        </p>
                    </li>
                    <li class="lbut">
                        <p class="pt">
                            <a class="abut" href="javascript:;"  @click="butSubmitMm">立即登录</a>
                        </p>
                        <p class="pb">
                            <a class="al" href="javascript:;" @click="toPassWord">忘记密码？</a>
                            <a class="ar" href="javascript:;" @click="tabPasOrYzm">验证码登录</a>
                        </p>
                    </li>
                </ul>
                <ul class="log_con_ul ul_yzm" v-else>
                    <li>
                        <p class="pt">
                            <input class="inp" :class="isPhoneTrue?'act':''" maxlength="11"  v-model="mPhoneNum" type="text" placeholder="请输入手机号" @input="addPhone"/>
                        </p>
                        <p class="pb">
                            <span v-if="isPhoneTrue" class="stex">{{mPhoneErrText}}</span>
                            <span v-if="isPhoneNot" class="stex">该手机号尚未注册，点击<a style="color:#0B6BC0;" href="javascript:;" @click="newRegister">立即注册</a></span>
                        </p>
                    </li>
                    <li v-if="isImgYzm">
                        <p class="pt">
                            <input class="inp inpYzm" :class="isTexImgYzmTrue?'act':''" type="text" placeholder="图片验证码" v-model="imgCode"/>
                            <img class="iYzm" :src="mImgYzmUrl"  @click="refreshImg"/>
                        </p>
                        <p class="pb">
                            <span v-if="isTexImgYzmTrue" class="stex">验证码输入错误</span>
                        </p>
                    </li>
                    <li>
                        <p class="pt">
                            <input class="inp inpYzm" :class="isYzmTrue?'act':''" type="text" placeholder="6位数字验证码" maxlength="6" v-model="mYzmNum" @input="checkIdCode"/>
                            <span class="sYzm" v-if="!isYzmTime" :class="isYzmBut?'sYzmAct':''"  @click="getIdCode">获取验证码</span>
                            <span class="sYzm" v-else>{{countdown}}s倒计时</span>
                        </p>
                        <p class="pb">
                            <span v-if="isYzmTrue" class="stex">验证码输入错误</span>
                        </p>
                    </li>
                    <li class="lbut">
                        <p class="pt">
                            <a class="abut" href="javascript:;" @click="butSubmitYzm">立即登录</a>
                        </p>
                        <p class="pb">
                            <a class="ar" href="javascript:;" @click="tabPasOrYzm">密码登录</a>
                        </p>
                    </li>
                </ul>
            </div>
            <div class="log_bot">
                <p class="log_bot_tex">
                    没有账号，请 <a class="ahref" href="javascript:;" @click="newRegister">注册</a>
                </p>
                <p class="log_bot_lin"></p>
            </div>
        </div>
    </div>
</template>
<script>
let verifyTimer = null
export default {
    data () {
        return {

            isLoginTab:true, //判断登录方式 true 为密码登录  false 为验证码登录

            isPhoneTrue: false, //判断手机号提示
            isPassTrue: false, //判断密码提示
            isYzmTrue:false,// 验证码提示

            mPhoneErrText:'',//手机号错误提示
            mPassErrText:'',//密码错误提示

            isPhoneNot: false, //手机号未注册
            
            isTexImgYzmTrue: false, //判断图片验证码提示
            isYzmBut: false, //判断验证码按钮高亮
            isYzmTime:false,//验证码倒计时
            countdown:30,//验证码倒计时

            mPhoneNum:'', //手机号
            mPassNum:'', //密码
            mYzmNum:'',//验证码

            isPhoneOrPassTrue:false,//手机号或密码不正确
            mPhoneOrPassErrText:'',//手机号或密码不正确文本

            isImgYzm: false, //判断显示图片验证码
            random: '?t=0', //图片验证码
            imgCode:'',//图片验证码输入框
            mImgYzmUrl:'',//图片验证码Url
        }
    },
    mounted(){
        
    },
    methods:{
        setCookie(name,value) { 
            var Days = 30; 
            var exp = new Date(); 
            exp.setTime(exp.getTime() + Days*24*60*60*1000); 
            document.cookie = name + "="+ escape (value) + ";expires=" + exp.toGMTString(); 
        } ,
        getCookie(cname){
          var name = cname + "=";
          var ca = document.cookie.split(';');
          for(var i=0; i<ca.length; i++) 
          {
            var c = ca[i].trim();
            if (c.indexOf(name)==0) return c.substring(name.length,c.length);
          }
          return "";
        },
        //跳转注册
        newRegister(){
            this.$router.push({
              path:'/register'
            })
        },
        //跳转注册
        toPassWord(){
            this.$router.push({
              path:'/password'
            })
        },
        //密码登录||验证码登录
        tabPasOrYzm(){
            if(this.isLoginTab){
                this.isLoginTab = false;
            }else{
                this.isLoginTab = true;
            }
            this.isYzmBut = false;
            this.isPhoneNot = false;
            this.mPhoneNum = '';
            this.mPassNum = '';
        },
        //手机号校验
        phoneReg(_phone){
            let that = this;
            var reg=11&& /^((13|14|15|16|17|18)[0-9]{1}\d{8})$/;//手机号正则验证
            var _phoneNum = _phone;
            if(!_phoneNum){//未输入手机号
                this.isPhoneTrue = true;
                this.mPhoneErrText = '请输入手机号';
                return;
            }
            if(!reg.test(_phoneNum)){//手机号不合法
                this.isPhoneTrue = true;
                this.mPhoneErrText = '手机格式错误';
                return;
            }
            this.isYzmBut = true;
            this.isPhoneTrue = false;
            this.mPhoneErrText = '';
        },
        //验证码
        yzmReg(_phone){
            let that = this;
            var reg =6&& /^[0-9]{6}$/;//验证码正则验证
            var _phoneNum = _phone;
            if(!_phoneNum){//未输入验证码
                this.isYzmTrue = true;
                return;
            }
            if(!reg.test(_phoneNum)){//验证码不合法
                this.isYzmTrue = true;
                return;
            }
            this.isYzmTrue = false;
        },
        //密码验证
        passReg(_phone){
            let that = this;
            var reg =/^(?![^a-zA-Z]+$)(?!\D+$).{6,12}$/;//验证码正则验证
            var _phoneNum = _phone;
            if(!_phoneNum){//未输入密码
                this.isPassTrue = true;
                this.mPassErrText = '请输入密码';
                return;
            }
            if(!reg.test(_phoneNum)){//密码不合法
                this.isPassTrue = true;
                this.mPassErrText = '密码格式不正确';
                return;
            }
            this.isPassTrue = false;
            this.mPassErrText = '';
        },
        //输入手机号验证
        addPhone(){
            this.phoneReg(this.mPhoneNum);
        },
         //密码验证
        checkPass(){
            if(!this.isPhoneTrue && this.mPhoneNum.length > 0){
                this.passReg(this.mPassNum);
            }
        },
        //输入验证码
        checkIdCode() {
            if(!this.isPhoneTrue && this.mPhoneNum.length > 0){
                this.yzmReg(this.mYzmNum);
            }
        },
        //点击验证码
        getIdCode() {
            if(this.imgCode.length > 0){
                this.getIdCodeXML({
                    "mobile": this.mPhoneNum,
                    "kaptcha": this.imgCode
                })
            }else{
                this.getIdCodeXML({
                    "mobile": this.mPhoneNum
                })
            }
        },
        //图形验证码
        refreshImg() {
            this.random = '?t=' + (new Date().getTime())
            this.mImgYzmUrl = process.env.BASE_URL+'/api/v1/account/kaptcha/' + this.mPhoneNum + this.random;
        },
        //点击验证码
        getIdCodeXML(params) {
            this.$axios.post('/api/v1/account/send_verify',params).then(res=>{
                console.log("res",res)
                if (res.data.status == 1) {
                    this.isYzmTime = true
                    this.isTexImgYzmTrue = false;
                    this.countdown = 30
                    verifyTimer = setInterval(() => {
                        this.countdown--
                        if (this.countdown == 0) {
                            clearInterval(verifyTimer)
                            verifyTimer = null
                            this.isYzmTime = false
                        }
                    }, 1000)
                } else if (res.data.status == 100004 || res.data.status == 100018) {
                    this.isImgYzm = true;
                    this.isTexImgYzmTrue = true;
                    this.imgCode = '';
                    this.refreshImg();
                } else {
                    
                }
            })
        },
        //验证码登录提交
        butSubmitYzm(){
            if(this.isImgYzm){
                if(this.imgCode.length > 0){
                    this.isTexImgYzmTrue = false;
                }else{
                    this.isTexImgYzmTrue = true;
                    return;
                }
            }
            if(this.mYzmNum.length > 0){
                this.isYzmTrue = false;
            }else{
                this.isYzmTrue = true;
                return;
            }
            let params = {
                "mobile": this.mPhoneNum,
                "verifyCode": this.mYzmNum
            }
            this.$axios.post('/api/v1/account/login',params).then(res=>{
                console.log("res",res)
                if(res.data.status == 1){
                    window.localStorage.setItem('accountId',res.data.data.accountId);
                    this.setCookie('user-token',res.data.data.token);
                    window.location.href = '/';
                }else if(res.data.status == 100011){
                    this.isPhoneNot = true;
                }else{
                    this.isYzmTrue = true;
                }
            })
        },
        //密码登录提交
        butSubmitMm(){
            let params = {
                "mobile": this.mPhoneNum,
                "password": this.mPassNum
            }
            this.$axios.post('/api/v1/account/login',params).then(res=>{
                console.log("res",res)
                if(res.data.status == 1){
                    window.localStorage.setItem('accountId',res.data.data.accountId);
                    this.setCookie('user-token',res.data.data.token);
                    window.location.href = '/';
                }else if(res.data.status == 100011){
                    this.isPhoneNot = true;
                }else{
                    this.isPhoneOrPassTrue = true;
                    this.mPhoneOrPassErrText = res.data.message;
                }
            })
        }
    }
}
</script>
<style scoped>
/* .container{
    width:100%;
    height:800px;
    position: relative;
    background:#F0F0F0;
    font-family:'PingFangSC-Regular','Microsoft YaHei';
} */
.login{
    width:100%;
    height:100%;
    position: fixed;
    left:0;
    top:0;
    z-index: 99;
}
.login .login_b{
    width:100%;
    height:100%;
    position:absolute;
    left:0;
    top:0;
    z-index:2;
    background:rgba(0, 0, 0, 0.6)
}
.login .login_c{
    width:90%;
    max-width:400px;
    height:auto;
    position:absolute;
    left:50%;
    top:50%;
    background:#fff;
    -webkit-transform: translate(-50%,-50%);
    -ms-transform: translate(-50%,-50%);
    transform: translate(-50%,-50%);
    z-index:3;
}
.login .login_c .log_tit{
    width:100%;
    height:64px;
    line-height:64px;
    text-align: center;
    color:#333333;
    font-size:20px;
    border-bottom:1px solid #EDEDED;
    position: relative;
}
.login .login_c .log_tit .i_close{
    color:#A0A0A0;
    font-size:24px;
    position:absolute;
    top:10px;
    right:10px;
    cursor:pointer;
}
.login .log_con{
    width:100%;
    height:auto;
    overflow:hidden;
}
.login .log_con .log_con_ul{
    width:80%;
    margin:26px auto 0;
    height:auto;
    overflow:hidden;
}
.login .log_con .log_con_ul li{
    width:100%;
    height:auto;
    overflow:hidden;
}
.login .log_con .log_con_ul li .pt{
    width:100%;
    height:40px;
    box-sizing:border-box;
    overflow:hidden;
}

.login .log_con .log_con_ul li .pt .inp{
    width:100%;
    padding:0 5%;
    height:40px;
    line-height:40px;
    color:#000000;
    font-size:14px;
    border:0;
    outline: none;
    box-sizing: border-box;
    border:1px solid #EDEDED;
    border-radius:3px;
    overflow:hidden;
}
.login .log_con .log_con_ul li .pt .act{
    border:1px solid #FF4144;
}
.login .log_con .log_con_ul li .pt .abut{
    display:block;
    width:100%;
    height:40px;
    line-height: 40px;
    text-align: center;
    color:#fff;
    font-size:16px;
    background:#F65D22;
    border-radius: 3px;
}
.login .log_con .log_con_ul li .pb{
    width:100%;
    height:24px;
    line-height:24px;
    overflow:hidden;
}
.login .log_con .log_con_ul li .pb .stex{
    display:block;
    font-size:12px;
    color:#FF3435;
}
.login .log_con .log_con_ul .lbut{
    margin-top:6px;
}
.login .log_con .log_con_ul .lbut .pt{
    border:0;
    border-radius:0;
}
.login .log_con .log_con_ul .lbut .pb{
    margin-top:12px;
}
.login .log_con .log_con_ul .lbut .pb .al{
    float:left;
    height:24px;
    line-height:24px;
    color:#6D6D6D;
    font-size:13px;
}
.login .log_con .log_con_ul .lbut .pb .ar{
    float:right;
    height:24px;
    line-height:24px;
    color:#6D6D6D;
    font-size:13px;
}
.login .log_con .ul_yzm li .pt .inpYzm{
    width:55%;
    float:left;
}
.login .log_con .ul_yzm li .pt .sYzm{
    width:40%;
    float:right;
    height:40px;
    line-height:40px;
    text-align: center;
    background:#EBEBEB;
    border-radius:3px;
    color:#B8B8B8;
    font-size:14px;
}
.login .log_con .ul_yzm li .pt .iYzm{
    width:40%;
    float:right;
    height:40px;
    border-radius:3px;
    border:0;
    cursor:pointer;
}
.login .log_con .ul_yzm li .pt .sYzmAct{
    background:#F65D22;
    color:#fff;
    cursor: pointer;
}


.login .log_bot{
    width:100%;
    height:22px;
    line-height: 22px;
    margin-top:22px;
    margin-bottom:32px;
    position:relative;
    text-align: center;
    color:#6D6D6D;
    font-size:14px;
}
.login .log_bot .ahref{
    color:#F65D22;
}
.login .log_bot .log_bot_tex{
    position:absolute;
    left:50%;
    top:0;
    width:140px;
    height:22px;
    margin-left:-70px;
    background:#fff;
    z-index:2;
}
.login .log_bot .log_bot_lin{
    position:absolute;
    left:50%;
    top:50%;
    width:80%;
    margin-left:-40%;
    height:1px;
    background:#EDEDED;
}
</style>