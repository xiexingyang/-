<template>
  <div class="context">
    <div class="top_box">
      <el-button type="primary" icon="edit" @click="dialogFormVisible = true"
        >新增图文</el-button
      >
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
      <el-table-column
        label="ID"
        prop="id"
        sortable
        width="100px"
      ></el-table-column>
      <el-table-column label="图文内容" width="400px">
        <template slot-scope="scope" class="tuwen">
          <div class="outbox">
            <img :src="scope.row.cover" style="height: 50px" />
            <div>
              <p>{{ scope.row.title }}</p>
              <p class="price">￥{{ scope.row.price }}</p>
            </div>
          </div>
        </template>
      </el-table-column>
      <el-table-column
        label="订阅量"
        prop="sub_count"
        width="150px"
      ></el-table-column>
      <el-table-column label="状态" width="150px">
        <template slot-scope="scope">
          <div class="shangjia" v-if="scope.row.status == 1">已上架</div>
          <div class="xiajia" v-if="scope.row.status == 0">已下架</div>
        </template>
      </el-table-column>
      <el-table-column
        label="创建时间"
        prop="created_time"
        width="300px"
      ></el-table-column>
      <el-table-column label="操作">
          <template slot-scope="scope">
        <el-button type="primary" @click="goedit(scope.row.id)">编辑</el-button>
        <el-button class="upstore" v-if="scope.row.status==0" type="" @click="changestatus(scope.row)">上架</el-button>
        <el-button v-if="scope.row.status==1" type="" @click="changeStatus(scope.row)">下架</el-button>
        <el-button type="warning" @click="deleteitem(scope.row.id)">删除</el-button>
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
    <el-dialog title="新增图文" :visible.sync="dialogFormVisible" width="50%">
      <el-form >
        <el-form-item label="标题">
          <el-input
            v-model="title"
            placeholder="请输入标题"
          ></el-input>
        </el-form-item>
        <el-form-item label="封面">
          <el-upload
            action="https://jsonplaceholder.typicode.com/posts/"
            list-type="picture-card"
            :on-preview="handlePictureCardPreview"
            :on-remove="handleRemove"
          >
            <i class="el-icon-plus"></i>
          </el-upload>
        </el-form-item>
        <el-form-item label="试看内容">
          <Edit class="fuwenben"></Edit>
        </el-form-item>
        <el-form-item label="课程内容">
          <Edit class="fuwenben"></Edit>
        </el-form-item>
        <el-form-item label="课程价格">
          <el-input-number
            v-model="num"
            :min="1"
            :max="999"
            label="描述文字"
          ></el-input-number>
        </el-form-item>
        <el-form-item label="状态">
          <el-radio-group v-model="status">
            <el-radio  label="上架"></el-radio>
            <el-radio label="下架"></el-radio>
          </el-radio-group>
        </el-form-item>
      </el-form>
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
import { fetchList, deleteMedia } from "../../api/media";
import Edit from "../../components/Tinymce/index";
export default {
  data() {
    return {
    status:"",
      num: 1,
      datalist: [],
      keyword: "",
      select: "",
      query: "",
      pagenum: 1,
      pagesize: 2,
      total: 0,
      dialogFormVisible: false,
        title:"",
      dialogImageUrl: "",
      dialogVisible: false,
    };
  },
  components: {
    Edit,
  },
  created() {
    this.getdatalist();
  },
  methods: {
    getdatalist() {
      fetchList().then((res) => {
        console.log(res.data.items);
        this.datalist = res.data.items;
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
    goedit(){},
    //点击下架/上架
    changeStatus(e){
    e.status=0
    },
    changestatus(e){
    e.status=1
    }
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
.upstore{
    background-color: green;
    color: white;
}
</style>