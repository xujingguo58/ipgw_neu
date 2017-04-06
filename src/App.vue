<template>
  <div id="app">  
    <mt-field label="用户名" placeholder="请输入用户名" v-model="username"></mt-field>
    <mt-field label="密码" placeholder="请输入密码" type="password" v-model="password"></mt-field>
    <input type="hidden" name="action" value="login">
    <input type="hidden" name="ac_id" value="1">
    <input type="hidden" name="user_ip" value="">
    <input type="hidden" name="nas_ip" value="">
    <input type="hidden" name="user_mac" value="">
    <input type="hidden" name="url" value="" ></br>
    <div id="switch">
    <mt-switch :value.sync="saveme" v-model="saveme">记住我</mt-switch> </br>
    </div>
    <div id='context'>
    <mt-button type="primary" @click="loginIn">连接</mt-button>
    <mt-button type="danger" @click="loginOut">断开</mt-button> 
     </div>
     </br>
    <mt-field label="time" readonly=true v-model="time"></mt-field>
    <mt-field label="use" readonly=true v-model="use"></mt-field>
    <mt-field label="money" readonly=true v-model="money"></mt-field>
    <mt-field label="ip" readonly=strue v-model="ip"></mt-field>
  </div>
</template>

<script>
import { MessageBox } from 'mint-ui';
export default {
  name: 'app',
  data () {
    return {
      action: 'login',
      username: '',
      password: '',
      response: '',
      saveme: true,
      info_arr: [],
      info_str: '',
      time: '',
      use: '',
      money: '',
      ip:'',
      popupVisible: false,
      logout_state: false

    }
  },
  created: function(){
        if(localStorage['ipgw_username']!=''&&localStorage['ipgw_password']!=''){
          this.username=localStorage['ipgw_username']
          this.password=localStorage['ipgw_password']
        }
  },
  methods: {
      format_time: function(sec){
        var h=Math.floor(sec/3600);
        var m=Math.floor((sec%3600)/60);
        var s=sec%3600%60;
        var out="";
        if(h<10)
        {
          out += "0"+h+" : ";
        }
        else
        {
          out+=h+" : ";
        }
        
        if(m<10)
        {
          out+="0"+m+" : ";
        }
        else
        {
          out+=m+" : ";
        }
        
        if(s<10)
        {
          out += "0"+s+"";
        }
        else
        {
          out += s+"";
        }
        return out;
    },
    format_flux: function(byte)//格式化流量
    {
        if(byte>(1024*1024*1024))
              return (this.format_number((byte/(1024*1024*1024)),2)+"G");
        if(byte>(1024*1024))
          return (this.format_number((byte/(1024*1024)),2)+"M");
        if(byte>1024)
          return (this.format_number((byte/1024),2)+"K");
        return byte+"b";
    },
    format_number:function(num, count){
      var n=Math.pow(10, count);
      var t=Math.round(num*n);
      return t/n;
    },
    get_online_info: function(){
      var k = Math.floor(Math.random() * ( 100000 + 1));
            var str=''
      var d = "action=get_online_info&key="+k;
      //alert(d);
      $.ajax({type: "post",
      url: "/include/auth_action.php?k="+k,
      data: d,
      async : false,

      success: function(res) {
      //显示在线信息，可根据需求扩展
        console.log(res)
        str=res  
      }
      });
    return str
    },
    check_save: function(){
      if(this.saveme){
          if (typeof(Storage) !== "undefined") {
            localStorage.setItem("ipgw_username", this.username)
            localStorage.setItem("ipgw_password", this.password)
          }
          else{
              alert('sorry your webBrowser not support HTML5 localStorage')
              this.save_me=false
          }
        }
        else{
          //MessageBox('nosave','s')
          localStorage.setItem("ipgw_username", '')
          localStorage.setItem("ipgw_password", '')
        }
    },
    loginIn: function(){
        this.check_save()
        let url = '/include/auth_action.php'
        var loading=false
        $.post(url, {
            action: this.action,
            username: this.username,
            password: this.password,
            ac_id:$("input[name='ac_id']").val(),
            user_mac:$("input[name='user_mac']").val(),
            user_ip:$("input[name='user_ip']").val(),
            nas_ip:$("input[name='nas_ip']").val(),
            save_me: 1,
            ajax: 1
            }, function(res1){
              //console.log(res1)   
             var p = /^login_ok,/;
            if(p.test(res1))//认证成功，弹出小窗口
             {
              MessageBox('login success')
              loading=true      
             } 
            else{
              MessageBox(res1)
            }
           })
          this.info_str=this.get_online_info()
          this.info_arr=this.info_str.split(",")
          this.time=this.format_time(this.info_arr[1])
          this.use=this.format_flux(this.info_arr[0])
          this.money=this.info_arr[2]+'yuan'
          this.ip=this.info_arr[5]
          loading=false
      //console.log(this.info_arr)
      /*console.log(this.format_time(this.info_arr[1]))
      console.log(this.format_flux(this.info_arr[0]))
      console.log(this.info_arr[2])
      console.log(this.info_arr[5])*/
    },
    loginOut:function(){

      $.post("/include/auth_action.php",{
      action: "logout",
      username: this.username,
      password: this.password,
      ajax: 1
      },function(res){
        this.popupVisible=true
            MessageBox('提示', 'logout');
      });
      //this.popupVisible=true
      this.logout_state=true
    }
  }
}
</script>

<style>
#context{
  text-align: center;
  margin: auto;
}
#switch{
  position: relative;
  left:65%;
}
/*#app{

  margin: auto;
  height: 60%;
  width: 15%;
}
#spinner{
  position: relative;
  top:50%;
  left: 3%;
}*/

</style>
