<template>
  <div class="app-container">
    <div class="filter-container">
      <el-input
        v-model="listQuery.studentName"
        placeholder="姓名"
        class="filter-item"
        clearable
        style="width: 300px"
        @keyup.enter="handleFilter"
      />
      <el-select
        v-model="listQuery.isChecked"
        placeholder="录取情况"
        class="filter-item"
        clearable
        style="width: 80px"
      >
        <el-option
          v-for="item in isCheckedOptions"
          :key="item.key"
          :label="item.display_name"
          :value="item.key"
        />
      </el-select>
      <el-select
        v-model="listQuery.academySearchInput"
        placeholder="学院名称"
        class="filter-item"
        clearable
        filterable
        style="width: 200px"
      >
        <el-option
          v-for="item in academyList"
          :key="item.key"
          :label="'(' + item.key + ')' + item.label"
          :value="item.key"
        />
      </el-select>
      <el-select
        v-model="listQuery.subjectCode"
        placeholder="专业代码"
        class="filter-item"
        clearable
        style="width: 200px"
      >
        <el-option
          v-for="item in subjectCodeOptions"
          :key="item.key"
          :label="item.display_name + '(' + item.key + ')'"
          :value="item.key"
        />
      </el-select>
      <el-select
        v-model="listQuery.sort"
        class="filter-item"
        style="width: 140px"
        @change="handleFilter"
      >
        <el-option
          v-for="item in sortOptions"
          :key="item.key"
          :label="item.label"
          :value="item.key"
        />
      </el-select>
      <el-button
        class="filter-item"
        icon="el-icon-search"
        type="primary"
        @click="handleFilter"
      >
        搜索
      </el-button>
      <el-button
        class="filter-item"
        icon="el-icon-refresh"
        type="primary"
        @click="handleFilterRefresh"
      >
        重置条件
      </el-button>
      <el-button
        class="filter-item"
        disabled
        icon="el-icon-edit"
        style="margin-left: 10px"
        type="primary"
        @click="handleCreate"
      >
        添加
      </el-button>
      <el-button
        :loading="downloadLoading"
        class="filter-item"
        icon="el-icon-download"
        type="primary"
        @click="handleDownload"
      >
        导出
      </el-button>
      <el-checkbox
        v-model="showMoreInfo"
        class="filter-item"
        style="margin-left: 15px"
        @change="tableKey += 1"
      >
        展示更多
      </el-checkbox>
    </div>

    <el-table
      :key="tableKey"
      v-loading="listLoading"
      :data="list"
      border
      fit
      highlight-current-row
      style="width: 100%"
      @sort-change="sortChange"
    >
      <el-table-column
        align="center"
        label="序号"
        sortable
        type="index"
        width="120px"
      />
      <el-table-column :label="'考生姓名'" min-width="150px">
        <template v-slot="{ row }">
          <span class="link-type" @click="handleUpdate(row)">{{
            row.studentName
          }}</span>
          <el-tag>{{ row.studentCode | subjectCodeFilter }}</el-tag>
        </template>
      </el-table-column>
      <el-table-column
        align="center"
        label="政治"
        prop="scorePolite"
        sortable
        width="80px"
      />
      <el-table-column
        align="center"
        label="英语"
        prop="scoreEnglish"
        sortable
        width="80px"
      />
      <el-table-column
        align="center"
        label="专业课一"
        prop="scoreProfessional1"
        sortable
        width="120px"
      />
      <el-table-column
        align="center"
        label="专业课二"
        prop="scoreProfessional2"
        sortable
        width="120px"
      />
      <el-table-column
        align="center"
        label="总分"
        prop="scoreTotal"
        sortable
        width="80px"
      />
      <el-table-column
        align="center"
        label="初试排名"
        prop="rank"
        sortable
        width="120px"
      />
      <el-table-column
        v-if="showMoreInfo"
        :label="'公共课总分'"
        align="center"
        sortable
        width="120px"
      >
        <template v-slot="{ row }">
          <span style="color: red">{{ row.scoreTotalPublic }}</span>
        </template>
      </el-table-column>
      <el-table-column
        v-if="showMoreInfo"
        :label="'专业课总分'"
        align="center"
        sortable
        width="120px"
      >
        <template v-slot="{ row }">
          <span style="color: red">{{ row.scoreTotalProfessional }}</span>
        </template>
      </el-table-column>
      <el-table-column
        v-if="showMoreInfo"
        :label="'专业名称'"
        align="center"
        sortable
        width="120px"
        prop="subjectName"
      />
      <el-table-column
        v-if="showMoreInfo"
        :label="'专业代码'"
        align="center"
        sortable
        width="110px"
      >
        <template v-slot="{ row }">
          <span style="color: red">{{ row.subjectCode }}</span>
        </template>
      </el-table-column>
      <el-table-column
        v-if="showMoreInfo"
        :label="'考生编号'"
        align="center"
        sortable
        width="140px"
      >
        <template v-slot="{ row }">
          <span style="color: red">{{ row.studentCode }}</span>
        </template>
      </el-table-column>
      <el-table-column
        v-if="showMoreInfo"
        :label="'备注'"
        align="center"
        width="110px"
      >
        <template v-slot="{ row }">
          <span style="color: red">{{ row.remark }}</span>
        </template>
      </el-table-column>
      <el-table-column
        :label="actions"
        align="center"
        class-name="small-padding fixed-width"
        width="280px"
      >
        <template v-slot="{ row }">
          <el-button size="mini" type="primary" @click="handleUpdate(row)">
            编辑
          </el-button>
          <el-button size="mini" type="default" @click="handleHide(row)">
            隐藏
          </el-button>
          <el-button
            disabled
            size="mini"
            type="danger"
            @click="handleDelete(row)"
          >
            删除
          </el-button>
        </template>
      </el-table-column>
    </el-table>

    <el-pagination
      v-show="total > 0"
      class="pagination"
      :current-page.sync="listQuery.page"
      :page-size.sync="listQuery.limit"
      :total="total"
      layout="total, prev, pager, next, sizes"
      @size-change="handlePageSizeChange"
      @current-change="handleCurrentChange"
    />

    <el-dialog
      :title="dialogStatus === 'create' ? '添加' : '编辑'"
      :visible.sync="dialogFormVisible"
    >
      <el-form
        ref="dataForm"
        :model="temp"
        :rules="rules"
        label-position="left"
        label-width="140px"
        style="width: 400px; margin-left: 50px"
      >
        <el-form-item label="复试线主键" prop="id">
          <el-input
            v-model="temp.id"
            :disabled="true"
            placeholder="请输入复试线主键"
          />
        </el-form-item>
        <el-form-item label="初试排名" prop="rank">
          <el-input
            v-model="temp.rank"
            :disabled="true"
            placeholder="请输入初试排名"
          />
        </el-form-item>
        <el-form-item label="学生姓名" prop="studentName">
          <el-input
            v-model="temp.studentName"
            :disabled="true"
            placeholder="请输入学生姓名"
          />
        </el-form-item>
        <el-form-item label="学生编号" prop="studentCode">
          <el-input
            v-model="temp.studentCode"
            :disabled="true"
            placeholder="请输入学生编号"
          />
        </el-form-item>
        <el-form-item label="学科代码" prop="subjectCode">
          <el-input
            v-model="temp.subjectCode"
            :disabled="true"
            placeholder="请输入学科代码"
          />
        </el-form-item>
        <el-form-item label="学科名称" prop="subjectName">
          <el-input
            v-model="temp.subjectName"
            :disabled="true"
            placeholder="请输入学科名称"
          />
        </el-form-item>
        <el-form-item label="政治" prop="scorePolite">
          <el-input v-model="temp.scorePolite" placeholder="请输入政治" />
        </el-form-item>
        <el-form-item label="英语" prop="scoreEnglish">
          <el-input v-model="temp.scoreEnglish" placeholder="请输入英语" />
        </el-form-item>
        <el-form-item label="专业课一" prop="scoreProfessional1">
          <el-input
            v-model="temp.scoreProfessional1"
            placeholder="请输入专业课一"
          />
        </el-form-item>
        <el-form-item label="专业课二" prop="scoreProfessional2">
          <el-input
            v-model="temp.scoreProfessional2"
            placeholder="请输入专业课二"
          />
        </el-form-item>
        <el-form-item label="初试总分" prop="scoreTotal">
          <el-input
            v-model="temp.scoreTotal"
            :disabled="true"
            placeholder="请输入初试总分"
          />
        </el-form-item>
        <el-form-item label="初试公共课总分" prop="scoreTotalPublic">
          <el-input
            v-model="temp.scoreTotalPublic"
            :disabled="true"
            placeholder="请输入初试公共课总分"
          />
        </el-form-item>
        <el-form-item label="初试专业课总分" prop="scoreTotalProfessional">
          <el-input
            v-model="temp.scoreTotalProfessional"
            :disabled="true"
            placeholder="请输入初试专业课总分"
          />
        </el-form-item>
        <el-form-item label="备注" prop="remark">
          <el-input v-model="temp.remark" placeholder="请输入备注" />
        </el-form-item>
      </el-form>
      <div slot="footer" class="dialog-footer">
        <el-button @click="dialogFormVisible = false">取消</el-button>
        <el-button
          type="primary"
          @click="dialogStatus === 'create' ? createData() : updateData()"
        >
          {{ dialogStatus === "create" ? "添加" : "编辑" }}
        </el-button>
      </div>
    </el-dialog>
  </div>
</template>

<script lang="ts">
import { ref, onMounted, defineComponent } from "vue";
import axios from "axios";
import * as XLSX from "xlsx";

const subjectCodeOptions = [
  { key: "100700", display_name: "药学" },
  { key: "100701", display_name: "生物科学" },
  // 其他专业代码
];

const isCheckedOptions = [
  { key: "0", display_name: "录取" },
  { key: "1", display_name: "落榜" },
];

const sortOptions = [
  { key: "0", label: "高分优先" },
  { key: "1", label: "低分优先" },
];

export default defineComponent({
  name: "QueryReviewListAllTable",

  setup() {
    const tableKey = ref(0);
    const list = ref<any[]>([]);
    const total = ref(0);
    const listLoading = ref(true);
    const listQuery = ref({
      page: 1,
      limit: 10,
      subjectCode: "",
      isChecked: undefined,
      studentName: "",
      academySearchInput: "",
      sort: "0",
    });
    const academyList = ref<any[]>([]);
    const showMoreInfo = ref(false);
    const temp = ref({
      id: undefined,
      rank: undefined,
      studentName: "",
      studentCode: "",
      subjectCode: "",
      subjectName: "",
      scorePolite: "",
      scoreEnglish: "",
      scoreProfessional1: "",
      scoreProfessional2: "",
      scoreTotal: "",
      scoreTotalPublic: "",
      scoreTotalProfessional: "",
      remark: "",
    });
    const dialogFormVisible = ref(false);
    const dialogStatus = ref("");
    const downloadLoading = ref(false);
    const rules = {
      /* your validation rules here */
    };

    const getList = async () => {
      listLoading.value = true;
      try {
        const response = await axios.get("/dev-api/ReviewListAll/list", {
          params: listQuery.value,
        });
        list.value = response.data.data.records;
        total.value = response.data.data.total;
      } catch (error) {
        console.error(error);
      } finally {
        listLoading.value = false;
      }
    };

    const handleFilter = () => {
      listQuery.value.page = 1;
      getList();
    };

    const handleFilterRefresh = () => {
      listQuery.value.page = 1;
      listQuery.value.studentName = "";
      getList();
    };

    const handleCreate = () => {
      resetTemp();
      dialogStatus.value = "create";
      dialogFormVisible.value = true;
    };

    const handleUpdate = (row: any) => {
      temp.value = Object.assign({}, row);
      dialogStatus.value = "update";
      dialogFormVisible.value = true;
    };

    const createData = async () => {
      // Logic for creating data
    };

    const updateData = async () => {
      // Logic for updating data
    };

    const handleHide = (row: any) => {
      // Logic for hiding
    };

    const handleDelete = (row: any) => {
      // Logic for deleting
    };

    const handleDownload = async () => {
      downloadLoading.value = true;
      const worksheet = XLSX.utils.json_to_sheet(list.value);
      const workbook = XLSX.utils.book_new();
      XLSX.utils.book_append_sheet(workbook, worksheet, "Sheet1");
      XLSX.writeFile(workbook, "所有专业复试名单.xlsx");
      downloadLoading.value = false;
    };

    const handleCurrentChange = (page: number) => {
      listQuery.value.page = page;
      getList();
    };

    const handlePageSizeChange = (size: number) => {
      listQuery.value.limit = size;
      listQuery.value.page = 1; // Reset to first page
      getList();
    };

    const resetTemp = () => {
      temp.value = {
        id: undefined,
        rank: undefined,
        studentName: "",
        studentCode: "",
        subjectCode: "",
        subjectName: "",
        scorePolite: "",
        scoreEnglish: "",
        scoreProfessional1: "",
        scoreProfessional2: "",
        scoreTotal: "",
        scoreTotalPublic: "",
        scoreTotalProfessional: "",
        remark: "",
      };
    };

    onMounted(() => {
      getList();
      // Fetch academy list, etc.
    });

    return {
      tableKey,
      list,
      total,
      listLoading,
      listQuery,
      academyList,
      subjectCodeOptions,
      isCheckedOptions,
      sortOptions,
      showMoreInfo,
      temp,
      dialogFormVisible,
      dialogStatus,
      downloadLoading,
      rules,
      handleFilter,
      handleFilterRefresh,
      handleCreate,
      handleUpdate,
      createData,
      updateData,
      handleHide,
      handleDelete,
      handleDownload,
      handleCurrentChange,
      handlePageSizeChange,
    };
  },
});
</script>

<style scoped>
.app-container {
  padding: 20px;
}

.filter-container {
  margin-bottom: 20px;
}

.link-type {
  cursor: pointer;
  color: blue;
}

.dialog-footer {
  text-align: right;
}

.fixed-width {
  width: 200px;
}

.small-padding {
  padding: 5px;
}

.pagination {
  margin-top: 20px;
  text-align: right;
}
</style>
