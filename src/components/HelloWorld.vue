<template>
  <div class="hello">
    <div class="bg">
      <img class="top_image" :src="TopImage" fit="cover" />

      <img class="bot_image" :src="BotImage" fit="cover" />
    </div>

    <div class="des_view">

      <!-- 提示框 -->
      <div class="diloag_view" @click="hide_diloag" v-if="diloag_toggle" >
        <div>
          <p class="diloag_view_title" >提示</p>
          <p class="diloag_view_des" >{{error_msg}}</p>
          <p class="diloag_view_footer" @click="hide_diloag" >确定</p>
        </div>
      </div>
      <!-- 登录成功 -->

      <div class="login_view" v-if="people_type==4">
        <img :src="Login" fit="cover" />
        <p>登录成功</p>
      </div>
      <!-- 老师自己扫 -->
      <div class="people_self" v-if="people_type==1">
        <img :src="qrode" fit="cover" />

        <p class="people_self_text">扫码成功</p>
        <div class="people_self_bot" @click="to_send(1)">确认登录</div>
      </div>
      <!-- 家长 -->
      <div class="parent_view" v-if="people_type==3">
        <div class="view_title">
          <img :src="ColorsImage" fit="cover" />
          <span>选择孩子</span>
        </div>

        <!-- 孩子列表 -->
        <div class="child_view">
          <div
            :class="cindex==index?'child_view_action':''"
            @click="check_child(index)"
            v-for="(item,index) in child_list"
            :key="index"
          >
            <img :src="item.avatar" fit="cover" />
            <p>{{item.sname}}</p>
            <span>{{item.class_name}}</span>
          </div>
        </div>

        <!-- 确定登录 -->

        <div @click="to_send(3)" :class="['send_data',cindex==-1?'no_check':'']">确认登录</div>
      </div>

      <!-- 教师 -->
      <div class="teacher_view" v-if="people_type==2">
        <div class="student_view_parent">
        <div class="teacher_search_view" @click="show_search">
          <img :src="SearchIcon" fit="cover" />

          <span>搜索学生</span>
        </div>

        <div class="view_title">
          <img :src="ColorsImage" fit="cover" />
          <span>选择年级班级</span>
        </div>

        <div class="class_view">
          <div
            :class="sindex==index?'action_class':''"
            v-for="(item,index) in student_list "
            :key="index"
            @click="change_list(index)"
          >{{item.class_name}}</div>
        </div>

        <div class="view_title">
          <img :src="ColorsImage" fit="cover" />
          <span>选择学生</span>
        </div>
        
          <div class="student_view">
            <div
              :class="stundent_num==index?'action_class':''"
              @click="change_student_list(index)"
              v-for="(item,index) in student_list[sindex].class_data "
              :key="index"
            >
              <img :src="item.avatar" fit="cover" />
              <span>{{item.sname}}</span>
            </div>
          </div>
        </div>
        <div class="send_data" @click="to_send(2)">确认登录</div>
      </div>

      <!-- 搜索页面 -->

      <div class="search_view" v-if="search_toggle">
        <div class="top_search">
          <div class="top_search_left">
            <img :src="SearchIcon" fit="cover" />
            <input type="text" v-model="search_value" />
            <img v-if="search_value!=''" :src="closeicon" fit="cover" @click="clear_value" />
          </div>

          <span @click="show_search">取消</span>
        </div>

        <!-- 搜索学生列表 -->
        <div class="search_student_list_parent">
          <div class="search_student_list">
            <div v-for="(item,index) in search_list" :key="index" @click="check_student(index)">
              <div class="search_student_list_left">
                <img :src="item.avatar" fit="cover" />
                <div class="search_student_list_left_text">
                  <span>{{item.name}}</span>
                  <span>{{item.class_name}}</span>
                </div>
              </div>
              <img :src="stundent_check_num==index?right_check_icon:checkicon" fit="cover" />
            </div>
          </div>
        </div>
        <div class="send_data" v-if="stundent_check_num!=-1" @click="to_send(-1)">确认登录</div>
      </div>
    </div>
  </div>
</template>

<script>
export default {
  name: "HelloWorld",
  data() {
    return {
      msg: "Welcome to Your Vue.js App",
      BotImage: require("../assets/bot_icon.png"),
      Login: require("../assets/login.png"),
      search_list: [],
      stundent_check_num: -1,
      search_value: "",
      ColorsImage: require("../assets/colors.png"),
      TopImage: require("../assets/top_icon.png"),
      SearchIcon: require("../assets/search_icon.png"),
      checkicon: require("../assets/check_icon.png"),
      right_check_icon: require("../assets/right_check_icon.png"),
      closeicon: require("../assets/close.png"),
      qrode: require("../assets/qrcode.png"),
      search_toggle: false,
      stundent_num: 0,
      student_list: [{ class_data: [] }],
      sindex: 0,
      post_url:
        "https://sc.imofang.cn/cube.aspx?c=mp&module_name=aits&lr=0&at=surestatus&user_type=0&oc_id=w_0_0_1595397188728&sid=4649&status=1",
      cindex: 0,
      people_type: 0,
      teacher: [
        {
          cid: 5,
          class_name: "小小班玫瑰班",
          class_data: [
            { sid: "18", sname: "李锐", avatar: "/smallfeet/0/default.png" },
            {
              sid: "66",
              sname: "成成",
              avatar: "/mcs/4/1/5/66/head_avatar.png"
            },
            {
              sid: "2988",
              sname: "李莉",
              avatar: "/mcs/4/1/5/2988/head_avatar.png"
            },
            {
              sid: "2992",
              sname: "陈大大",
              avatar: "/mcs/4/1/5/2992/head_avatar.png"
            },
            {
              sid: "3379",
              sname: "鲁班",
              avatar: "/smallfeet/0/default.png"
            },
            {
              sid: "3449",
              sname: "锤子",
              avatar: "/smallfeet/0/default.png"
            },
            { sid: "4152", sname: "米米", avatar: "/mcs/0/girl.png" },
            { sid: "4153", sname: "霞霞", avatar: "/mcs/0/girl.png" },
            { sid: "4327", sname: "李锦", avatar: "/mcs/0/girl.png" },
            { sid: "4328", sname: "李标", avatar: "/mcs/0/girl.png" },
            { sid: "4341", sname: "骆赖皮", avatar: "/mcs/0/boy.png" },
            {
              sid: "4342",
              sname: "李佳的",
              avatar: "/mcs/4/1/5/4342/head_avatar.png"
            },
            {
              sid: "4415",
              sname: "李咖",
              avatar: "/smallfeet/0/default.png"
            },
            { sid: "4436", sname: "方子乔", avatar: "/mcs/0/boy.png" },
            {
              sid: "4445",
              sname: "王恩",
              avatar: "/mcs/4/1/5/4445/head_avatar.png"
            },
            { sid: "4654", sname: "joker", avatar: "/mcs/0/girl.png" },
            {
              sid: "4787",
              sname: "鹿易",
              avatar: "/mcs/4/1/5/4787/head_avatar.png"
            },
            {
              sid: "4788",
              sname: "王迪迦",
              avatar: "/mcs/4/1/5/4788/head_avatar.png"
            }
          ]
        },
        {
          cid: 38,
          class_name: "小一班",
          class_data: [
            {
              sid: "3330",
              sname: "小红",
              avatar: "/mcs/3/1/38/3330/head_avatar.png"
            },
            {
              sid: "3380",
              sname: "格格",
              avatar: "/smallfeet/0/default.png"
            },
            {
              sid: "3650",
              sname: "李莉",
              avatar: "/smallfeet/0/default.png"
            },
            {
              sid: "3668",
              sname: "李三",
              avatar: "/smallfeet/0/default.png"
            },
            { sid: "3842", sname: "谭杰2", avatar: "/mcs/0/girl.png" },
            { sid: "3847", sname: "李悠", avatar: "/mcs/0/girl.png" },
            { sid: "4288", sname: "李嗷嗷", avatar: "/mcs/0/girl.png" },
            { sid: "4296", sname: "李蔚蓝", avatar: "/mcs/0/girl.png" },
            { sid: "4598", sname: "谭杰", avatar: "/mcs/0/boy.png" },
            { sid: "4599", sname: "李佳的", avatar: "/mcs/0/girl.png" },
            { sid: "4640", sname: "王恩", avatar: "/mcs/0/girl.png" },
            { sid: "4641", sname: "王杰谭", avatar: "/mcs/0/girl.png" },
            { sid: "4642", sname: "成成", avatar: "/mcs/0/girl.png" },
            { sid: "4643", sname: "王者耀", avatar: "/mcs/0/girl.png" }
          ]
        },
        {
          cid: 155,
          class_name: "中班玫瑰班",
          class_data: [
            { sid: "4649", sname: "王小恒", avatar: "/mcs/0/girl.png" },
            { sid: "4650", sname: "李彩", avatar: "/mcs/0/girl.png" }
          ]
        }
      ],
      child_list: [],
      url:"",
      parent_arr: [
        {
          sid: 4436,
          sname: "方子乔",
          avatar: "/mcs/0/boy.png",
          class_name: "小小班玫瑰班"
        },
        {
          sid: 4612,
          sname: "方子茹",
          avatar: "/mcs/0/girl.png",
          class_name: ""
        },
        {
          sid: 4649,
          sname: "王小恒",
          avatar: "/mcs/0/girl.png",
          class_name: "中班玫瑰班"
        },
        {
          sid: 4687,
          sname: "方剑雄",
          avatar: "/mcs/28/1/166/4687/head_avatar.png",
          class_name: "一年级107班"
        }
      ],
      error_msg:"",
      diloag_toggle:false
    };
  },
  watch: {
    search_value(val) {
      let { student_list, search_list } = this;
      search_list = [];
      if (val) {
        for (let i in student_list) {
          for (let j in student_list[i].class_data) {
            if (student_list[i].class_data[j].sname.indexOf(val) != -1) {
              search_list.push({
                name: student_list[i].class_data[j].sname,
                class_name: student_list[i].class_name,
                sid: student_list[i].class_data[j].sid,
                avatar: student_list[i].class_data[j].avatar,
                is_check: false
              });
            }
          }
        }
      } else {
        this.stundent_check_num = -1;
      }

      this.search_list = search_list;
    }
  },
  methods: {
    check_child(index) {
      this.cindex = index;
    },
    hide_diloag(){
      let {diloag_toggle}=this
      this.diloag_toggle=!diloag_toggle
    },
    to_send(num) {
      console.log(num);
      let { people_type,url } = this;
      let sid = 0,
        status = 1,
        user_type=0,
        code=0,
        msg="";
        user_type=people_type==1?1:0
      if (num == 1) {
      } else if (num == 2) {
        let { stundent_num, sindex, student_list } = this;
        sid = student_list[sindex].class_data[stundent_num].sid;
        code=student_list[sindex].class_data[stundent_num].code
        msg=student_list[sindex].class_data[stundent_num].msg

      } else if (num == 3) {
        let { child_list, cindex } = this;
        sid = child_list[cindex].sid;
        code= child_list[cindex].code
        msg= child_list[cindex].msg

      } else if (num == -1) {
        let { search_list, stundent_check_num } = this;
        sid = search_list[stundent_check_num].sid;
        code= search_list[stundent_check_num].code
        msg= search_list[stundent_check_num].msg

      }

      if(code==1){
        this.error_msg=msg
        this.hide_diloag()
        return
      }
      if (num != 1) {
        url = `${url}&sid=${sid}&status=1`;
      } else {
        url = `${url}&status=1`;
      }
      console.log(url);
      var a = document.createElement("a");
      a.target = "_self";
      a.href = url;
      a.click();
    },

    check_student(index) {
      this.stundent_check_num = index;
    },
    clear_value() {
      this.search_value = "";
    },
    change_list(index) {
      this.sindex = index;
      this.stundent_num = 0;
    },
    change_student_list(index) {
      this.stundent_num = index;
    },

    show_search() {
      let { search_toggle } = this;
      this.search_toggle = !search_toggle;
    }
  },
  created() {},
  mounted() {
    let config_data = null;
    console.log(window.config_data)
    if (process.env.NODE_ENV == "development") {
      let { parent_arr, teacher } = this;
      let num = 2;
      config_data = {
        type: num,
        data: num == 3 ? parent_arr : num == 2 ? teacher : [],
        url:""
      };
    } else {
      config_data = JSON.parse(window.config_data);
    }

    let type = config_data.type;
    this.people_type = type;
    this.url=config_data.url
    console.log(config_data);
    if (type == 3) {
      // 家长扫码
      let child_list = config_data.data;
      console.log(child_list);
      for (let i in child_list) {
        child_list[i].avatar = `http://files.imofang.cn${child_list[i].avatar}`;
      }
      this.child_list = child_list;
    } else if (type == 2) {
      // 老师扫码
      let student_list = config_data.data;
      for (let i in student_list) {
        for (let j in student_list[i].class_data) {
          student_list[i].class_data[
            j
          ].avatar = `http://files.imofang.cn${student_list[i].class_data[j].avatar}`;
        }
      }
      this.student_list = student_list;
    }
  }
};
</script>

<!-- Add "scoped" attribute to limit CSS to this component only -->
<style scoped>
.head_class {
  font-size: 30px;
}
.hello {
  position: relative;
}
.teacher_search_view {
  display: flex;
  align-items: center;
  width: 675px;
  height: 75px;
  background: rgba(253, 254, 255, 1);
  box-shadow: 0px 1px 38px 0px rgba(73, 73, 73, 0.1);
  border-radius: 38px;
  margin: 25px auto;
  padding: 0 28px;
  box-sizing: border-box;
  font-size: 26px;
  font-weight: 400;
  color: rgba(213, 213, 213, 1);
}

.teacher_search_view img {
  width: 38px;
  height: 38px;
  margin-right: 19px;
}
.bg {
  width: 100vw;
  height: 100vh;
  position: absolute;
  top: 0;
  left: 0;
  z-index: 5;
}
.des_view {
  position: absolute;
  top: 0;
  left: 0;
  width: 100vw;
  height: 100vh;
  display: flex;
  flex-direction: column;
  padding: 0 36px;
  box-sizing: border-box;
  z-index: 9;
}
.bot_image {
  width: 253px;
  height: 314px;
  position: absolute;
  bottom: 0;
  left: 0;
}
.view_title {
  display: flex;
  align-items: center;
  position: relative;
  height: 50px;
  padding-left: 46px;
  box-sizing: border-box;
  font-size: 30px;
  font-weight: 600;
  color: rgba(75, 75, 75, 1);
  text-shadow: 0px 1px 5px rgba(116, 116, 116, 0.5);
  margin-bottom: 30px;
}
.child_view {
  display: flex;
  flex-wrap: wrap;
  justify-content: space-between;
  padding: 0 40px;
  box-sizing: border-box;
  margin-top: 30px;
}
.child_view > div {
  width: 250px;
  background: rgba(253, 254, 255, 1);
  box-shadow: 0px 1px 38px 0px rgba(73, 73, 73, 0.1);
  border-radius: 25px;
  border: 1px solid rgba(236, 237, 241, 1);
  padding: 23px 31px;
  box-sizing: border-box;
  margin-top: 38px;
}
.child_view > div > img {
  width: 88px;
  height: 88px;
  box-shadow: 0px 3px 19px 0px rgba(0, 0, 0, 0.2);
  border-radius: 50%;
  margin-bottom: 25px;
}
.child_view > div > p {
  font-size: 26px;
  font-weight: 600;
  color: rgba(32, 32, 32, 1);
  margin-bottom: 4px;
}
.child_view > div > span {
  font-size: 18px;
  font-weight: 400;
  color: rgba(32, 32, 32, 1);
}
.child_view_action {
  background: #00ad56 !important;
}
.child_view_action > img {
  box-shadow: none !important;
}
.child_view_action > span {
  color: #fff !important;
}
.child_view_action > p {
  color: #fff !important;
}
.view_title img {
  height: 50px;
  width: 72px;
  position: absolute;
  left: 0;
  top: 0;
  z-index: 2;
}
.send_data {
  width: 525px;
  height: 88px;
  background: linear-gradient(
    360deg,
    rgba(0, 173, 86, 1) 0%,
    rgba(0, 214, 142, 1) 100%
  );
  border-radius: 59px;
  display: flex;
  align-items: center;
  justify-content: center;
  color: #fff;
  font-size: 30px;
  /* margin: 0 auto;
margin-top: 300px; */
  position: absolute;
  bottom: 63px;
  left: 50%;
  transform: translate(-50%, 0);
}
.view_title span {
  height: 50px;
  display: flex;
  align-items: center;

  position: absolute;
  left: 46px;
  top: 0;
  z-index: 5;
}
.top_image {
  width: 283px;
  height: 183px;
  position: absolute;
  top: 0;
  right: 0;
}
.parent_view {
  display: flex;
  flex-direction: column;
  padding-top: 80px;
  box-sizing: border-box;
}
.no_check {
  background: #d5d5d5 !important;
}
.class_view,
.student_view {
  display: flex;
  flex-wrap: wrap;
  margin-bottom: 30px;
}
.student_view > div {
  width: 213px;
  height: 88px;
  display: flex;
  align-items: center;
  padding: 0 15px;
  box-shadow: 0px 1px 38px 0px rgba(73, 73, 73, 0.1);
  border: 1px solid rgba(236, 237, 241, 1);
  border-radius: 25px;
  box-sizing: border-box;
  font-size: 26px;
  font-weight: 400;
  color: rgba(189, 189, 189, 1);
  margin-bottom: 13px;
}
.student_view > div img {
  width: 63px;
  height: 63px;
  box-shadow: 0px 3px 19px 0px rgba(0, 0, 0, 0.2);
  border-radius: 50%;
  margin-right: 15px;
}
.student_view > div:nth-child(3n-1) {
  margin-left: 15px;
  margin-right: 15px;
}
.class_view > div {
  /* width: 213px; */
  height: 75px;
  background: rgba(253, 254, 255, 1);
  box-shadow: 0px 1px 38px 0px rgba(73, 73, 73, 0.1);
  padding: 0 23px;
  box-sizing: border-box;
  border-radius: 25px;
  border: 1px solid rgba(236, 237, 241, 1);
  display: flex;
  align-items: center;
  justify-content: center;
  font-size: 30px;
  margin-bottom: 19px;
}
.class_view > div img {
  width: 63px;
  height: 63px;
  box-shadow: 0px 3px 19px 0px rgba(0, 0, 0, 0.2);
}
.class_view > div:nth-child(3n-1) {
  margin-left: 5px;
  margin-right: 5px;
}
.search_view {
  position: fixed;
  z-index: 6666;
  height: 100vh;
  width: 100vw;
  top: 0;
  left: 0;
  background: #fff;
  padding: 25px 38px;
  box-sizing: border-box;
  display: flex;
  flex-direction: column;
  align-content: flex-start;
}
.top_search {
  display: flex;
  align-items: center;
  width: 100%;
  justify-content: space-between;
}
.top_search > span {
  font-size: 30px;
  font-weight: 500;
  color: rgba(32, 32, 32, 1);
}
.top_search_left {
  display: flex;
  align-items: center;
  width: 575px;
  height: 75px;
  background: rgba(253, 254, 255, 1);
  box-shadow: 0px 1px 38px 0px rgba(73, 73, 73, 0.1);
  border-radius: 38px;
  padding: 0 25px;
  box-sizing: border-box;
}
.top_search_left img {
  width: 38px;
  height: 38px;
  margin-right: 19px;
}
.top_search_left input {
  width: 450px;
  border: none;
  height: 100%;
  font-size: 26px;
  font-weight: 400;
  color: rgba(32, 32, 32, 1);
}
.student_view_parent {
  height: 86vh;
  overflow: scroll;
}
.top_search_left input:focus {
  outline: none;
}
.search_student_list {
  width: 100%;
  display: flex;
  flex-direction: column;
  margin-top: 30px;
}
.search_student_list_parent {
  max-height: 80vh;
  overflow: scroll;
}
.search_student_list > div {
  width: 100%;
  height: 125px;
  display: flex;
  align-items: center;
  justify-content: space-between;
  border-bottom: 1px solid #ecedf1;
}
.search_student_list > div > img {
  width: 45px;
  height: 45px;
}
.search_student_list_left {
  display: flex;
  align-items: center;
}
.search_student_list_left img {
  width: 75px;
  height: 75px;
  box-shadow: 0px 3px 19px 0px rgba(0, 0, 0, 0.2);
  margin-right: 21px;
  border-radius: 50%;
}
.search_student_list_left_text {
  display: flex;
  flex-direction: column;
  justify-content: space-between;
  height: 75px;
}
.diloag_view{
  width: 100%;
  height: 100vh;
  position: fixed;
  top: 0;
  left: 0;
  z-index: 9999;
  background:rgba(0,0,0,0.3);
  display: flex;
  align-items: center;
  justify-content: center;
}
.diloag_view>div{
  background: #fff;
  width: 50%;
  border-radius: 15px;
  padding-top: 15px;
  box-sizing: border-box;
  display: flex;
  flex-direction: column;
  align-items: center;
}
.diloag_view_title{
  font-size: 30px;
  font-weight:bold;

}
.diloag_view_des{
  height: 100px;
  align-items: center;
  display: flex;
  justify-content: center;
  font-size: 30px;
  
}
.diloag_view_footer{
  width: 100%;
  height: 70px;
  display: flex;
  align-items: center;
  justify-content: center;
font-size:32px;
font-weight:500;
color:rgba(151,152,168,1);
border-top: 1px solid #BABBCD;
}
.search_student_list_left_text span:nth-child(1) {
  font-size: 26px;
  font-weight: 500;
  color: rgba(32, 32, 32, 1);
}
.search_student_list_left_text span:nth-child(2) {
  font-size: 18px;
  font-weight: 400;
  color: rgba(32, 32, 32, 1);
}
.action_class {
  background: #00ad56 !important;
  color: #fff !important;
}
.people_self {
  display: flex;
  flex-direction: column;
  align-items: center;
  justify-content: center;
  height: 100vh;
  width: 100%;
}
.people_self img {
  width: 500px;
  height: 500px;
  margin-bottom: 28px;
}
.people_self_text {
  font-size: 32px;
  font-weight: 400;
  color: rgba(182, 186, 255, 1);
  margin-bottom: 82px;
}
.people_self_bot {
  width: 320px;
  height: 100px;
  background: rgba(84, 93, 255, 1);
  box-shadow: 0px 18px 54px 0px rgba(84, 93, 255, 0.3);
  border-radius: 20px;
  display: flex;
  align-items: center;
  justify-content: center;
  font-size: 32px;
  font-weight: 500;
  color: rgba(248, 248, 255, 1);
}
.login_view {
  width: 100%;
  height: 100vh;
  display: flex;
  flex-direction: column;
  justify-content: center;
  align-items: center;
  font-size: 45px;
  font-weight: 500;
  color: rgba(0, 173, 86, 1);
  line-height: 63px;
}
.login_view img {
  width: 488px;
  height: 488px;
  margin-bottom: 55px;
}
</style>
