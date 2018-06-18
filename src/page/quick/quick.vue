<template>
  <div>
		<div class="m-b-20 ovf-hd">

			<div class="fl w-200 m-l-30">
				<el-input placeholder="请输入用户名" v-model="keywords" size="small">
					<el-button slot="append" icon="search" @click="search()"></el-button>
				</el-input>
			</div>
		</div>
    <el-table
      :data="tableData"
      stripe
      style="width: 100%">
      <el-table-column
        prop="id"
        label="工号"
        width="180">
      </el-table-column>
      <el-table-column
        prop="name"
        label="姓名"
        width="180">
      </el-table-column>
      <el-table-column
        prop="auth"
        label="权限">
      </el-table-column>
    </el-table>

  </div>
</template>

<script>
  import http from '../../api/http'
  export default {
    data() {
      return {
        // tableData是嵌入的数据
        tableData: []
      }
    },
    methods: {
      search() {
        router.push({ path: this.$route.path, query: { keywords: this.keywords, page: 1 }})
      },
      selectItem(val) {
        this.multipleSelection = val
      },
      handleCurrentChange(page) {
        router.push({ path: this.$route.path, query: { keywords: this.keywords, page: page }})
      },
      confirmDelete(item) {
        this.$confirm('确认删除该用户?', '提示', {
          confirmButtonText: '确定',
          cancelButtonText: '取消',
          type: 'warning'
        }).then(() => {
          _g.openGlobalLoading()
          this.apiDelete('admin/users/', item.id).then((res) => {
            _g.closeGlobalLoading()
            this.handelResponse(res, (data) => {
              _g.toastMsg('success', '删除成功')
              setTimeout(() => {
                _g.shallowRefresh(this.$route.name)
              }, 1500)
            })
          })
        }).catch(() => {
          // catch error
        })
      },
      getAllUsers() {
        this.loading = true
        const data = {
          params: {
            keywords: this.keywords,
            page: this.currentPage,
            limit: this.limit
          }
        }
        this.apiGet('http://localhost:8888/users', data).then((res) => {
          console.log('res = ', _g.j2s(res))
          this.handelResponse(res, (data) => {
            this.tableData = data
            this.dataCount = data
          })
        })
      },
      getCurrentPage() {
        let data = this.$route.query
        if (data) {
          if (data.page) {
            this.currentPage = parseInt(data.page)
          } else {
            this.currentPage = 1
          }
        }
      },
      getKeywords() {
        let data = this.$route.query
        if (data) {
          if (data.keywords) {
            this.keywords = data.keywords
          } else {
            this.keywords = ''
          }
        }
      },
      init() {
        this.getKeywords()
        this.getCurrentPage()
        this.getAllUsers()
      }
    },
    created() {
      this.init()
    },
    computed: {
      addShow() {
        return _g.getHasRule('users-save')
      },
      editShow() {
        return _g.getHasRule('users-update')
      },
      deleteShow() {
        return _g.getHasRule('users-delete')
      }
    },
    watch: {
      '$route' (to, from) {
        this.init()
      }
    },
    components: {
      btnGroup
    },
    mixins: [http]
  }
</script>