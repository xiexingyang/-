<template>
  <div class="context">
    <div class="top_box">
      <el-dropdown>
        <el-button type="danger">
          黑名单设置<i class="el-icon-arrow-down el-icon--right"></i>
        </el-button>
        <el-dropdown-menu slot="dropdown">
          <el-dropdown-item>禁止评论</el-dropdown-item>
          <el-dropdown-item>禁止访问</el-dropdown-item>
        </el-dropdown-menu>
      </el-dropdown>
      <div class="inner_box">
        <el-select v-model="select" placeholder="请选择活动区域">
          <el-option label="区域一" value="shanghai"></el-option>
          <el-option label="区域二" value="beijing"></el-option>
        </el-select>
        <el-input v-model="keyword" placeholder="请输入"></el-input>
        <el-button type="primary" icon="search">查询</el-button>
      </div>
    </div>
    <el-table :data="datalist" border>
      <el-table-column type="selection" width="55"></el-table-column>
      <el-table-column label="用户" width="400px">
        <template slot-scope="scope" class="tuwen">
          <div class="outbox">
            <img :src="scope.row.user.avatar" style="height: 50px" />
            <div>
              <p>{{ scope.row.user.username }}</p>
            </div>
          </div>
        </template>
      </el-table-column>
      <el-table-column
        label="消费总额"
        prop="total_consume"
        width="150px"
      ></el-table-column>
      <el-table-column
        label="创建时间"
        prop="created_time"
        width="300px"
      ></el-table-column>
      <el-table-column label="操作" width="600px">
        <template slot-scope="scope">
          <el-button type="primary" @click="goedit(scope.row)">详情</el-button>
          <el-button type="success">联系用户</el-button>
          <el-dropdown>
            <el-button type="danger" style="margin: 20px">
              黑名单设置<i class="el-icon-arrow-down el-icon--right"></i>
            </el-button>
            <el-dropdown-menu slot="dropdown">
              <el-dropdown-item>禁止评论</el-dropdown-item>
              <el-dropdown-item>禁止访问</el-dropdown-item>
            </el-dropdown-menu>
          </el-dropdown>
        </template>
      </el-table-column>
    </el-table>
    <el-pagination
      @size-change="handleSizeChange"
      @current-change="handleCurrentChange"
      :current-page="pagenum"
      :page-sizes="[1, 2, 5, 10]"
      :page-size="pagesize"
      layout="total, sizes, prev, pager, next, jumper"
      :total="total"
    >
    </el-pagination>
    <!-- 新增图文 -->
    <el-dialog title="用户详情" :visible.sync="dialogFormVisible" width="80%">
      <el-row>
        <el-col :span="6">
          <p>ID:{{ Detailslist.id }}</p>
          <p>锁定:{{ Detailslist.status == 1 ? "是" : "否" }}</p>
        </el-col>
        <el-col :span="6">
          <p>用户名:{{ Detailslist.username }}</p>
          <p>会员:{{ Detailslist.user_level }}</p>
        </el-col>
        <el-col :span="6">
          <p>昵称:{{ Detailslist.nickname || "未设置" }}</p>
          <p>会员到期时间:{{ Detailslist.user_level_expire }}</p>
        </el-col>
        <el-col :span="6">
          <p>手机号:{{ Detailslist.phone }}</p>
          <p>注册时间{{ Detailslist.created_time }}</p>
        </el-col>
      </el-row>
      <div>
        <el-tabs v-model="activeName" @tab-click="handleClick">
          <el-tab-pane label="课程订阅" name="first">
            <el-table :data="UserCourse" border height="300px">
              <el-table-column label="课程标题" prop="title"></el-table-column>
              <el-table-column label="购买价格" prop="price"></el-table-column>
              <el-table-column
                label="购买时间"
                prop="created_time"
              ></el-table-column>
            </el-table>
            <el-pagination
              class="innerpage"
              background
              layout="prev, pager, next"
              :total="1000"
            >
            </el-pagination>
          </el-tab-pane>
          <el-tab-pane label="专栏订阅" name="second">
            <el-table :data="UserColumn" border height="300px">
              <el-table-column label="课程标题" prop="title"></el-table-column>
              <el-table-column label="购买价格" prop="price"></el-table-column>
              <el-table-column
                label="购买时间"
                prop="created_time"
              ></el-table-column>
            </el-table>
            <el-pagination
              class="innerpage"
              background
              layout="prev, pager, next"
              :total="1000"
            >
            </el-pagination>
          </el-tab-pane>
          <el-tab-pane label="订单记录" name="third">
            <el-table :data="UserOrder" border height="300px">
              <el-table-column label="ID" prop="id"></el-table-column>
              <el-table-column label="订单号" prop="no"></el-table-column>
              <el-table-column label="购买价格" prop="price"></el-table-column>
              <el-table-column label="状态">{{
                status == "success" ? "成功" : "待支付"
              }}</el-table-column>
              <el-table-column label="商品" prop="title"></el-table-column>
              <el-table-column
                label="购买时间"
                prop="created_time"
              ></el-table-column>
            </el-table>
            <el-pagination
              class="innerpage"
              background
              layout="prev, pager, next"
              :total="1000"
            >
            </el-pagination>
          </el-tab-pane>
          <el-tab-pane label="观看历史" name="fourth">
            <el-table :data="UserHistory" border height="300px">
              <el-table-column label="课程标题" prop="title"></el-table-column>
              <el-table-column label="课程类型" prop="type"></el-table-column>
              <el-table-column
                label="学习时长"
                prop="total_time"
              ></el-table-column>
            </el-table>
            <el-pagination
              class="innerpage"
              background
              layout="prev, pager, next"
              :total="1000"
            >
            </el-pagination>
          </el-tab-pane>
          <el-tab-pane label="用户评论" name="fifth">
            <el-table :data="UserComment" border height="300px">
              <el-table-column label="评论内容" prop="content"></el-table-column>
              <el-table-column label="评论时间" prop="created_time"></el-table-column>
              <el-table-column
                label="课程标题"
                prop="title"
              ></el-table-column>
              <el-table-column label="类型" prop="type"></el-table-column>
            </el-table>
            <el-pagination
              class="innerpage"
              background
              layout="prev, pager, next"
              :total="1000"
            >
            </el-pagination>
          </el-tab-pane>
        </el-tabs>
      </div>

      <div slot="footer" class="dialog-footer">
        <el-button @click="dialogFormVisible = false">取 消</el-button>
        <el-button type="primary" @click="dialogFormVisible = false"
          >提交</el-button
        >
      </div>
    </el-dialog>
  </div>
</template>

<script>
import {
  fetchList,
  fetchUserCourse,
  fetchUserColumn,
  fetchUserOrder,
  fetchUserHistory,
  fetchUserComment,
} from "../../api/user";
import Edit from "../../components/Tinymce/index";
export default {
  data() {
    return {
      status: "",
      num: 1,
      datalist: [],
      keyword: "",
      select: "",
      query: "",
      pagenum: 1,
      pagesize: 2,
      total: 0,
      dialogFormVisible: false,
      title: "",
      dialogImageUrl: "",
      dialogVisible: false,
      Detailslist: [],
      activeName: "first",
      UserCourse: [],
      UserColumn: [],
      UserOrder: [],
      UserHistory: [],
      UserComment: [],
    };
  },
  components: {
    Edit,
  },
  created() {
    this.getdatalist();
    this.getUserCourse();
    this.getUserColumn();
    this.getUserOrder();
    this.getUserHistory();
    this.getUserComment();
  },
  methods: {
    getdatalist() {
      fetchList().then((res) => {
        console.log(res.data.items);
        this.datalist = res.data.items;
      });
    },
    //获取课程订阅
    getUserCourse() {
      fetchUserCourse().then((res) => {
        this.UserCourse = res.data.items;
      });
    },
    //获取专栏订阅
    getUserColumn() {
      fetchUserColumn().then((res) => {
        this.UserColumn = res.data.items;
      });
    },
    //获取订单记录
    getUserOrder() {
      fetchUserOrder().then((res) => {
        this.UserOrder = res.data.items;
      });
    },
    //观看历史
    getUserHistory() {
      fetchUserHistory().then((res) => {
        this.UserHistory = res.data.items;
      });
    },
    //用户评价
    getUserComment() {
      fetchUserComment().then((res) => {
        console.log(res.data.items);
        this.UserComment = res.data.items;
      });
    },

    //监听pagesize改变的事件
    handleSizeChange(newSize) {
      // console.log(newSize);
      this.pagesize = newSize;
      this.getdatalist();
    },
    //监听 页码值 改变的事件
    handleCurrentChange(newPage) {
      this.pagenum = newPage;
      this.getdatalist();
    },
    handleRemove(file, fileList) {
      console.log(file, fileList);
    },
    handlePictureCardPreview(file) {
      this.dialogImageUrl = file.url;
      this.dialogVisible = true;
    },
    //删除
    deleteitem() {
      deleteMedia().then((res) => {
        console.log(res);
      });
    },
    //点击编辑
    goedit(e) {
      this.dialogFormVisible = true;
      this.Detailslist = e.user;
      console.log(e);
    },
    //点击下架/上架
    changeStatus(e) {
      e.status = 0;
    },
    changestatus(e) {
      e.status = 1;
    },
    handleClick(tab, event) {
      console.log(tab, event);
    },
  },
};
</script>

<style  scope>
.context {
  padding: 20px;
}
.top_box {
  display: flex;
  justify-content: space-between;
  padding: 30px;
}
.inner_box {
  display: flex;
}
.outbox {
  display: flex;
}
.price {
  color: red;
}
.shangjia {
  background-color: rgb(231, 250, 240);
  color: green;
  width: 50px;
}
.xiajia {
  width: 50px;
  background-color: rgb(255, 237, 237);
  color: #ff0000;
}
.avatar-uploader .el-upload {
  border: 1px dashed #d9d9d9;
  border-radius: 6px;
  cursor: pointer;
  position: relative;
  overflow: hidden;
}
.avatar-uploader .el-upload:hover {
  border-color: #409eff;
}
.avatar-uploader-icon {
  font-size: 28px;
  color: #8c939d;
  width: 178px;
  height: 178px;
  line-height: 178px;
  text-align: center;
}
.avatar {
  width: 178px;
  height: 178px;
  display: block;
}
.fuwenben {
  margin-top: 50px;
}
.upstore {
  background-color: green;
  color: white;
}
.innerpage {
  margin-top: 60px;
}
</style>