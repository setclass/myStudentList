<template>
  <div style="display: flex; justify-content: space-between; height: 100%">
    <div class="left" style="width: 20%; height: 100%; overflow: hidden">
      <el-row class="tac" style="height: 100%">
        <el-col :span="12" style="height: 100%; width: 100%">
          <el-menu
            style="height: 100%"
            default-active="2"
            class="el-menu-vertical-demo"
            @open="handleOpen"
            @close="handleClose"
            background-color="#545c64"
            text-color="#fff"
            active-text-color="#ffd04b"
          >
            <el-submenu index="1">
              <template slot="title">
                <i class="el-icon-location"></i>
                <span>导航一</span>
              </template>
              <el-menu-item-group>
                <template slot="title">分组一</template>
                <el-menu-item index="1-1">选项1</el-menu-item>
                <el-menu-item index="1-2">选项2</el-menu-item>
              </el-menu-item-group>
            </el-submenu>
          </el-menu>
        </el-col>
      </el-row>
    </div>
    <div class="table" style="width: 75%">
      <div>
        <el-dialog
          title="提示"
          :visible.sync="dialogVisible"
          width="40%"
          :before-close="handleClose"
        >
          <el-upload
            class="upload-demo"
            action="http://127.0.0.1:3000/users/upload"
            :on-success="successUpload"
            :show-file-list="false"
            multiple
            :limit="3"
            :file-list="fileList"
          >
            <el-button size="small" type="primary">点击上传</el-button>
            <div slot="tip" class="el-upload__tip">
              <p>只能上传jpg/png文件，且不超过500kb</p>
              <img :src="uploadImg" alt="" class="img" />
            </div>
          </el-upload>
          <el-input
            v-model="name"
            placeholder="请输入商品名称"
            class="put"
          ></el-input>
          <el-input
            type="textarea"
            :rows="2"
            placeholder="请输入商品描述"
            v-model="describe"
            class="put"
          >
          </el-input>

          <span slot="footer" class="dialog-footer">
            <el-button @click="dialogVisible = false">取 消</el-button>
            <el-button
              v-bind:style="sure"
              type="primary"
              class="edbtn"
              @click="addData"
              >确 定</el-button
            >
            <el-button
              v-bind:style="editorBtn"
              class="edbtn"
              type="primary"
              @click="editor"
              >编 辑</el-button
            >
          </span>
        </el-dialog>
      </div>

      <el-table :data="tableData" border style="width: 100%">
        <el-table-column label="图片" width="180">
          <template slot-scope="scope">
            <img
              :src="scope.row.img"
              alt=""
              style="width: 100px; height: 100px"
            />
          </template>
        </el-table-column>
        <el-table-column prop="name" label="姓名" width="180">
        </el-table-column>
        <el-table-column prop="describe" label="描述"> </el-table-column>
        <el-table-column label="操作">
          <template slot-scope="scope">
            <el-button type="primary" @click="add" size="mini">添加</el-button>
            <el-button size="mini" @click="edit(scope.$index)">编辑</el-button>
            <el-button size="mini" type="danger" @click="del(scope.row._id)"
              >删除</el-button
            >
          </template>
        </el-table-column>
      </el-table>
      <div class="block">
        <el-pagination
          layout="prev, pager, next"
          :page-size="limit"
          :total="total"
          :current-page="page"
          @current-change="currentChange"
        >
        </el-pagination>
      </div>
    </div>
  </div>
</template>

<script>
import axios from "axios";
export default {
  data() {
    return {
      editorBtn: {
        display: "none",
      },
      sure: {
        display: "block",
      },
      dialogVisible: false,
      fileList: [
        {
          name: "",
          url: "",
        },
      ],
      _id: "",
      uploadImg: "",
      name: "",
      describe: "",
      tableData: [],
      total:0,
      limit:4,
     page:1
    };
  },
  created() {
    this.getStudentData()
  },

  methods: {
    handleOpen(key, keyPath) {},
    handleClose(key, keyPath) {},
    currentChange(page){
      this.getStudentData(page)
    },
    //获取数据
    getStudentData(page){
       axios.get("http://127.0.0.1:3000/users/obtain", {
        params: {
          page:page,
          limit:4
        },
      })
      .then((res) => {
        this.tableData = res.data.data;
        this.total=res.data.count;
        this.page=res.data.page
        console.log(res)
      });
    },
    //删除
    del(_id,page) {
      axios
        .delete("http://127.0.0.1:3000/users/dele", {
          data: {
            _id: _id,
          },
        })
        .then((res,page) => {
          this.getStudentData(page)
        });
    },
    

    add() {
      this.dialogVisible = true;
      this.editorBtn.display = "none";
      this.sure.display = "block";
      this.name = "";
      this.uploadImg = "";
      this.describe = "";
    },
    

    edit(index) {
      this.dialogVisible = true;
      this.name = this.tableData[index].name;
      this.uploadImg = this.tableData[index].img;
      this.describe = this.tableData[index].describe;
      this._id = this.tableData[index]._id;
      this.editorBtn.display = "block";
      this.sure.display = "none";
    },

    handleClose(done) {
      this.$confirm("确认关闭？")
        .then((_) => {
          done();
        })
        .catch((_) => {});
    },
//添加
    addData() {
      axios
        .post("http://127.0.0.1:3000/users/content", {
          name: this.name,
          img: this.uploadImg,
          describe: this.describe,
        })
        .then((response) => {
          (this.name = ""), (this.uploadImg = ""), (this.describe = "");
         this.getStudentData();
        })
        .catch(function (error) {
          console.log(error);
        });
    },
    //编辑
    editor() {
      axios
        .put("http://127.0.0.1:3000/users/editor", {
          _id: this._id,
          name: this.name,
          img: this.uploadImg,
          describe: this.describe,
        })
        .then((res) => {
          if (res.data.code == 1) {
            alert("更新成功");
            this.dialogVisible = false;
            this.getStudentData();
          }
        })
        .catch((error) => {
          console.log(error);
        });
    },
    //上传
    successUpload(file, fileList) {
      this.uploadImg = "http://127.0.0.1:3000/upload/" + file.url;
      console.log(file, fileList);
    },
   
  },
};
</script>

<style>
.put {
  margin: 10px 0px;
}
.img {
  width: 100px;
  height: 100px;
}
.el-dialog__footer {
  position: relative;
}
.editorBtn {
  display: block;
}
.edbtn {
  position: absolute;
  left: 10px;
  bottom: 20px;
}
.cell {
  width: 210px;
}
</style>