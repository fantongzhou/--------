 <template>
  <el-container>
    <el-header style="background-color: white">
      <template>
        <el-container>
          <el-breadcrumb separator-class="el-icon-arrow-right">
            <el-breadcrumb-item>用户管理</el-breadcrumb-item>
            <el-breadcrumb-item>用户列表</el-breadcrumb-item>
          </el-breadcrumb>
          <el-header style="background-color: white">
            <el-form
              :inline="true"
              :model="formInline"
              class="demo-form-inline"
              size="mini"
            >
              <el-form-item label="用户名">
                <el-input
                  v-model="formInline.userName"
                  placeholder="用户名"
                  class="elInput"
                ></el-input>
              </el-form-item>
              <el-form-item>
                <el-button type="primary" class="btn" @click="onSubmit"
                  ><i class="el-icon-search"></i>查询</el-button
                >
              </el-form-item>
              <el-form-item>
                <el-button type="danger" class="btn" @click="resetting"
                  ><i class="el-icon-refresh"></i>重置</el-button
                >
              </el-form-item>
            </el-form>
          </el-header>
          <el-main class="elMain">
            <el-button @click="initAddUser" size="mini">添加</el-button>
            <el-table
              v-loading="loading"
              :data="tableData"
              stripe
              border
              size="mini"
            >
              <el-table-column v-if="false" prop="id"></el-table-column>
              <el-table-column
                align="center"
                label="用户名"
                prop="userName"
              ></el-table-column>
              <el-table-column
                align="center"
                label="密码"
                prop="password"
              ></el-table-column>
              <el-table-column align="center" label="操作">
                <template slot-scope="scope">
                  <el-button size="mini" @click="initEditUser(scope.row)"
                    >编辑</el-button
                  >
                  <el-button
                    size="mini"
                    type="danger"
                    @click="removeUserByuserName(scope.row.userName)"
                    >删除</el-button
                  >
                </template>
              </el-table-column>
            </el-table>
            <!-- 添加用户 -->
            <el-dialog
              :title="formTitle"
              :visible.sync="dialogFormVisible"
              width="580px"
            >
              <el-form :model="form">
                <el-form-item label="用户名" :label-width="formLabelWidth">
                  <el-input
                    v-model="form.userName"
                    autocomplete="off"
                    :disabled="istrue"
                  ></el-input>
                </el-form-item>
                <el-form-item label="密码" :label-width="formLabelWidth">
                  <el-input
                    v-model="form.password"
                    autocomplete="off"
                    show-password
                  ></el-input>
                </el-form-item>
              </el-form>
              <div slot="footer" class="dialog-footer">
                <el-button @click="dialogFormVisible = false">取 消</el-button>
                <el-button type="primary" @click="submitAddOrEditUser()"
                  >确 定</el-button
                >
              </div>
            </el-dialog>
          </el-main>

          <el-footer>
            <div style="padding: 15px 0; text-align: right">
              <el-pagination
                background
                @size-change="handleSizeChange"
                @current-change="handleCurrentChange"
                :current-page="formInline.pageNum"
                :page-sizes="pageSizes"
                :page-size="formInline.pageSize"
                layout="total, sizes, prev, pager, next, jumper"
                :total="pageTotal"
              >
              </el-pagination>
            </div>
          </el-footer>
        </el-container> </template></el-header
  ></el-container>
</template>
 
 <script>
import { commonAPI } from "@/api/commonAPI";
export default {
  data() {
    return {
      istrue: true,
      tableData: [],
      loading: false,
      formInline: {
        userName: "",
        pageNum: 1,
        pageSize: 10,
      },
      pageSizes: [5, 10, 15, 20],
      pageTotal: 0,
      dialogFormVisible: false,
      delVisible: false, //删除提示弹框的状态
      formTitle: "",
      form: {},
      flag: true, //添加修改标识，true：添加；false: 修改
      formLabelWidth: "80px",
    };
  },

  created() {
    this.getData();
  },
  methods: {
    getData() {
      (this.loading = true),
        commonAPI("queryUserList", this.formInline).then((res) => {
          this.loading = false;
          this.tableData = res.data.data.rows;
          this.pageTotal = res.data.data.total;
        });
    },
    onSubmit() {
      this.getData();
    },
    resetting() {
      this.formInline.userName = "";
      this.formInline.sex = "";
      this.getData();
    },
    handleSizeChange(val) {
      //console.log(`每页 ${val} 条`);
      this.formInline.pageSize = val;
      this.formInline.pageNum = 1;
      this.getData();
    },
    handleCurrentChange(val) {
      //console.log(`当前页: ${val}`);
      this.formInline.pageNum = val;
      this.getData();
    },
    //添加用户：初始化
    initAddUser() {
      this.form = {};
      this.formTitle = "添加用户";
      this.istrue = false;
      this.flag = true;
      this.dialogFormVisible = true;
    },
    //提交保存:添加或编辑用户
    submitAddOrEditUser() {
      console.log(methodName);
      console.log(this.form);
      commonAPI("editUser", this.form).then((res) => {
        this.dialogFormVisible = false;
        this.getData();
      });
      this.form = {};
    },
    //编辑用户:初始化
    initEditUser(row) {
      this.istrue = true;
      this.form = row;
      this.formTitle = "编辑用户";
      this.flag = false;
      this.dialogFormVisible = true;
    },
    //提交保存:添加或编辑用户
    submitAddOrEditUser() {
      let methodName = "editUser";
      if (this.flag) {
        methodName = "addUser";
      }
      commonAPI(methodName, this.form).then((res) => {
        this.form = {};
        this.dialogFormVisible = false;
        this.getData();
      });
    },
    //删除用户:根据id删除用户
    async removeUserByuserName(userName) {
      const confirmResult = await this.$confirm(
        "此操作将永久删除该用户, 是否继续?",
        "提示",
        {
          confirmButtonText: "确定",
          cancelButtonText: "取消",
          type: "warning",
        }
      ).catch((err) => err);
      // console.log(confirmResult);
      // 确认删除则返回字符串 confirm
      // 取消返回 cancel
      if (confirmResult !== "confirm") {
        return this.$message.info("已取消删除");
      }
      const { data: res } = await commonAPI(
        "deleteUserByuserName/" + userName,
        ""
      );
      // 刷新列表
      this.getData();
    },
  },
};
</script>
 
 <style scoped>
.elInput,
.btn {
  margin-top: 18px;
}
</style>
 <style>
.el-main {
  padding: 5px 10px;
}
.elMain {
  height: 418px;
}
.el-table th {
  height: 45px;
  font-size: 16px;
  font-family: 微软雅黑;
  font-weight: 500;
  color: darkblue;
}
</style>