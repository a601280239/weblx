<template>
      <div  >
        <el-container>
          <el-header> <div >
            <el-form :inline="true" class="demo-form-inline">
              <el-form-item label="审批人">
                <el-input v-model="where.name" placeholder="审批人"></el-input>
              </el-form-item>
              <el-form-item label="日期">
                <el-date-picker
                  v-model="dts"
                  type="daterange"
                  value-format="yyyy-MM-dd"
                  range-separator="至"
                  start-placeholder="开始日期"
                  end-placeholder="结束日期">
                </el-date-picker>
              </el-form-item>
              <el-form-item>
                <el-button type="primary"  size="mini" @click="getList()">查询</el-button>
                <el-button
                  size="mini"
                  type="danger"
                  @click="plsc()">批量删除</el-button>
                <el-button type="primary"  size="mini" @click="add()">添加</el-button>
              </el-form-item>

            </el-form>
          </div></el-header>
          <el-main>

            <div style="margin:0 auto">
              <template >
                <el-table
                  ref="multipleTable"
                  :data="userList"
                  border="true"
                  tooltip-effect="dark"
                  style="width:100%"
                  @selection-change="handleSelectionChange">
                  <el-table-column
                    type="selection"
                    width="55">
                  </el-table-column>
                  <el-table-column
                    prop="id"
                    label="序号"
                    width="120">
                  </el-table-column>
                  <el-table-column
                    prop="name"
                    label="姓名"
                    width="120">
                  </el-table-column>
                  <el-table-column
                    prop="age"
                    label="年龄"
                    width="120">
                  </el-table-column>
                  <el-table-column
                    prop="sex"
                    label="性别"
                    width="120">
                  </el-table-column>


                  <el-table-column
                    label="日期"
                    width="120">
                    <template slot-scope="scope">{{ changeDate(scope.row.dt) }}</template>
                  </el-table-column>
                  <el-table-column label="操作" cdcd>

                    <el-button
                      size="mini"
                      @click="handleEdit(scope.$index, scope.row)">编辑</el-button>
                    <el-button
                      size="mini"
                      type="danger"
                      @click="handleDelete(scope.$index, scope.row)">删除</el-button>

                  </el-table-column>

                </el-table>

              </template>
            </div>
            <div class="block" style="text-align:center" >

              <el-pagination
                @size-change="handleSizeChange"
                @current-change="handleCurrentChange"
                :current-page="pageInfo.pageNum"
                :page-sizes="[3, 5, 10, 20]"
                :page-size="pageInfo.pageSize"
                layout="total, sizes, prev, pager, next, jumper"
                :total="pageInfo.total">
              </el-pagination>
            </div>
          </el-main>
          <el-footer>
            <div >
              <el-dialog title="用户注册" :visible.sync="dialogFormVisible"
                         :before-close="handleClose">
                <el-form ref="form" :model="entitymod">
                  <el-form-item label="序号" v-if="entitymod.id!=0" :readonly="true">
                    <el-input v-model="entitymod.id"></el-input>
                  </el-form-item>


                  <el-form-item label="姓名">
                    <el-input v-model="entitymod.name"></el-input>
                  </el-form-item>
                  <el-form-item label="照片" >
                    <el-image
                      style="width: 50px; height: 50px"
                      :src="url+entitymod.path" v-if="entitymod.path!=null"></el-image>
                    <el-upload
                      class="avatar-uploader"
                      action="http://localhost:9999/api/upload"
                      :show-file-list="false"
                      :on-success="handleAvatarSuccess"
                      :before-upload="beforeAvatarUpload">
                      <img v-if="imageUrl" :src="imageUrl" class="avatar">
                      <i v-else class="el-icon-plus avatar-uploader-icon"></i>
                    </el-upload>
                  </el-form-item>
                  <el-form-item label="年龄">
                    <el-input v-model="entitymod.age"></el-input>
                  </el-form-item>
                  <el-form-item label="日期">
                    <el-date-picker
                      v-model="entitymod.dt"
                      value-format="yyyy-MM-dd"
                      type="date"
                      placeholder="选择日期">
                    </el-date-picker>
                  </el-form-item>

                  <el-form-item label="性别">
                    <el-radio-group v-model="entitymod.sex" >
                      <el-radio label="男"></el-radio>
                      <el-radio label="女" ></el-radio>
                    </el-radio-group>
                  </el-form-item>

                </el-form>
                <span slot="footer" class="dialog-footer">
    <el-button @click="guanbi()">关闭</el-button>
    <el-button type="primary" @click="save()">保存</el-button>
  </span>
              </el-dialog>
            </div>
          </el-footer>
        </el-container>




      </div>
</template>

<script>
  import  axios from 'axios'
    export default {
        name: "user",
      data() {
        return {
          userList:[],
          pageInfo:{
            pageSize:3,
          },
          where:{name:"",dt1:"",dt2:""},
          dts:[],
          ids:[],
          dialogFormVisible: false,
          entitymod:{},
          url:"http://localhost:8090/",
          imageUrl:""

        }
      },
      mounted(){
          this.getList(1,3);
      },
      methods: {
        getList(page,pageSize){
          if(page==null){
            page=1;
          }
          pageSize=this.pageInfo.pageSize;
          if(this.dts!=null&&this.dts.length>0){
              this.where.dt1=this.dts[0];
            this.where.dt2=this.dts[1];
          }
          axios.post("http://localhost:9999/api/list?name="+this.where.name+"&dt1="+this.where.dt1+"&dt2="+this.where.dt2+"&page="+page+"&pageSize="+pageSize).then((res)=>{
            this.userList=res.data.list
            console.log()
            this.pageInfo=res.data;
          })
        },
        handleSelectionChange(val) {
          for (var i = 0; i < val.length; i++) {
            this.ids[i]=val[i].id;
          }
        },
        changeDate(value){
          return moment(value).format("YYYY-MM-DD");
        },
        handleEdit(index, row) {
          this.entitymod=row;
          this.entitymod.dt=this.changeDate(this.entitymod.dt);
          this.imageUrl="";
          this.dialogFormVisible=true;
          console.log(index, row);
        },
        handleDelete: function (index, row) {
          axios.post("http://localhost:9999/api/delete?id=" + row.id).then((res) => {

            if (res.data > 0) {

              this.$message({
                message: '恭喜你，成功删除',
                type: 'success',
                duration:"1000",

              });
              this.getList(this.pageInfo.pageNum,this.pageInfo.pageSize);

            }else{
              this.$message({
                message: '删除失败',
                type: 'error',
                duration:"1000",
              });
            }

          })
          console.log(index, row);
        },
        handleSizeChange(val) {
          this.getList(1,val);
          console.log(`每页 ${val} 条`);
        },
        handleCurrentChange(val) {
          this.getList(val, this.pageInfo.pageSize);
          console.log(`当前页: ${val}`);
        },
        plsc(){
          if(this.ids.length==0){
            this.$message({
              message: '请选择你要删除的元素',
              type: 'error',
              duration:"1000",
            });
            return
          }

          console.log(this.ids);
          axios.post("http://localhost:9999/api/deletes?ids=" + this.ids).then((res) => {

            if (res.data > 0) {

              this.$message({
                message: '恭喜你，成功删除',
                type: 'success',
                duration:"1000",

              });
              this.getList(this.pageInfo.pageNum,this.pageInfo.pageSize);

            }else{
              this.$message({
                message: '删除失败',
                type: 'error',
                duration:"1000",
              });
            }

          })
        },
        add(){
          this.dialogFormVisible=true;
          this.entitymod={id:0};
        },
        handleClose(done) {
          this.getList(this.pageInfo.pageNum,this.pageInfo.pageSize);
          this.dialogFormVisible=false;
        },
        handleAvatarSuccess(res, file) {
          this.entitymod.path=file.name
          this.imageUrl = URL.createObjectURL(file.raw);
        },
        beforeAvatarUpload(file) {

          const isLt2M = file.size / 1024 / 1024 < 2;


          if (!isLt2M) {
            this.$message.error('上传头像图片大小不能超过 2MB!');
          }
          return isLt2M;
        },
        guanbi(){
          this.getList(this.pageInfo.pageNum,this.pageInfo.pageSize);
          this.dialogFormVisible=false;
        },
        save(){
          var url="http://localhost:9999/api/add";
          if(this.entitymod.id>0){
            alert(this.entitymod.id);
            url="http://localhost:9999/api/update"

          }
          console.log(this.entitymod);
          axios.post(url,this.entitymod).then((res) => {

            if (res.data > 0) {

              this.$message({
                message: '恭喜你，操作成功',
                type: 'success',
                duration:"1000",

              });
              this.getList(this.pageInfo.pageNum,this.pageInfo.pageSize);

            }else{
              this.$message({
                message: '操作失败',
                type: 'error',
                duration:"1000",
              });
            }

        }
        )}

      }



    }
</script>

<style scoped>
  .avatar-uploader .el-upload {
    border: 1px dashed #d9d9d9;
    border-radius: 6px;
    cursor: pointer;
    position: relative;
    overflow: hidden;
  }
  .avatar-uploader .el-upload:hover {
    border-color: #409EFF;
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
  .el-header, .el-footer {
    background-color: #B3C0D1;
    color: #333;
    text-align: center;
    line-height: 60px;
  }

  .el-aside {
    background-color: #D3DCE6;
    color: #333;
    text-align: center;
    line-height: 200px;
  }

  .el-main {
    background-color: #E9EEF3;
    color: #333;
    text-align: center;
    line-height: 160px;
  }

</style>
