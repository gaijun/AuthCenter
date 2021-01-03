<template>
  <el-card shadow="never">
      <div class="side-table-container">
          <div class="table-area full">
              <div class="action-filter">
                <div class="actions">
                    <el-button type="primary" size="small" @click="handleCreate">创建新客户端</el-button>
                </div>
                <div style="margin-left:50px" class="filters">
                    <el-input
                        v-model="queryForm.keywords"
                        size="small"
                        style="margin-right:5px"
                        placeholder="请输入关键字搜索"
                        prefix-icon="el-icon-search"
                        />
                    <el-button size="small" @click="handleSearch">搜索</el-button>
                </div>
              </div>
              <el-table
                :key="tableKey"
                v-loading="listLoading"
                :data="list"
                border
                fit
                highlight-current-row
                size="small"
                style="width: 100%;"
                @selection-change="handleSelectionChange"
                >
                <el-table-column type="selection" width="40" align="center" />
                <el-table-column
                    label="客户端id"
                    prop="clientId"
                    align="center"
                >
                    <template slot-scope="{ row }">
                    <span>{{ row.clientId | empty }}</span>
                    </template>
                </el-table-column>
                <el-table-column
                    label="客户端名称"
                    prop="clientName"
                    align="center"
                >
                    <template slot-scope="{ row }">
                    <span>{{ row.clientName | empty }}</span>
                    </template>
                </el-table-column>
                <el-table-column
                    label="操作"
                    prop="action"
                    align="center"
                    width="220"
                >
                  <template slot-scope="{ row }">
                      <el-button
                        size="mini"
                        type="primary"
                        icon="el-icon-edit"
                        @click="handleUpdate(row.id)"
                      >
                        编辑
                      </el-button>
                      <el-button
                        size="mini"
                        type="danger"
                        icon="el-icon-delete"
                        @click="handleDelete(row.id)"
                      >
                        删除
                      </el-button>
                  </template>
                </el-table-column>
              </el-table>
              <div class="bottom">
                <div>
                    已选{{ seletedDatas.length }}条
                </div>
                <pagination
                    v-show="total > 0"
                    :total="total"
                    :page.sync="queryForm.page"
                    :limit.sync="queryForm.limit"
                    @pagination="getList"
                />
              </div>
          </div>
      </div>
      <CreateClientDialog ref="createClientDialog" @success="handleCreateSuccess"></CreateClientDialog>
      <UpdateClientDialog ref="updateClientDialog" @success="handleUpdateSuccess"></UpdateClientDialog>
  </el-card>
</template>

<script>
import {
  getClients, deleteClient
} from '@/api/identity-server/client'
import baseListQuery from '@/utils/abp'
import Pagination from '@/components/Pagination'
import CreateClientDialog from './components/CreateClientDialog'
import UpdateClientDialog from './components/UpdateClientDialog'

export default {
    components: { Pagination, CreateClientDialog, UpdateClientDialog },
    data() {
    return {
      tableKey: 0,
      list: null,
      total: 0,
      listLoading: true,
      queryForm: Object.assign({
        keywords: undefined
      }, baseListQuery),
      seletedDatas: [],
    }
  },
  created() {
    this.getList()
  },
  methods: {
    handleSelectionChange(rows) {
      this.seletedDatas = rows
    },
    handleSearch(){
      this.getList()
    },
    handleCreate(){
      this.$refs.createClientDialog.showDialog()
    },
    handleCreateSuccess(){
      this.getList()
    },
    handleDelete(id){
      this.$confirm('确定要删除？', '确定', {
        confirmButtonText: '确定',
        cancelButtonText: '取消',
        type: 'warning'
      }).then(() => {
        deleteClient(id).then(res => {
          this.$notify({
              title: '成功',
              message: '操作成功',
              type: 'success',
              duration: 2000
          })
          this.getList()
        })
      }).catch(()=>{})
    },
    handleUpdate(id){
      this.$refs.updateClientDialog.showDialog(id)
    },
    handleUpdateSuccess(){
      this.getList()
    },
    getList() {
      this.listLoading = true
      getClients(this.queryForm).then(response => {
        this.list = response.items
        this.total = response.totalCount
        this.listLoading = false
      })
    },
  }
}
</script>

<style>

</style>