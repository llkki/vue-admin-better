<template>
  <div class="permissions-container">
    <el-divider content-position="left">
      intelligence模式,前端根据permissions拦截路由(演示环境,默认使用此方案)
    </el-divider>

    <el-form ref="form" :inline="true" :model="form">
      <el-form-item label="切换账号">
        <el-radio-group v-model="form.account">
          <el-radio label="admin">admin</el-radio>
          <el-radio label="editor">editor</el-radio>
          <el-radio label="test">test</el-radio>
        </el-radio-group>
      </el-form-item>

      <el-form-item>
        <el-button type="primary" @click="handleChangePermission">
          切换权限
        </el-button>
      </el-form-item>
      <el-form-item label="当前账号拥有的权限">
        {{ JSON.stringify(permissions) }}
      </el-form-item>
    </el-form>
    <el-divider content-position="left">按钮级权限演示</el-divider>
    <el-button v-permissions="['admin']" type="primary">
      我是拥有["admin"]权限的按钮
    </el-button>
    <el-button v-permissions="['editor']" type="primary">
      我是拥有["editor"]权限的按钮
    </el-button>
    <el-button v-permissions="['test']" type="primary">
      我是拥有["test"]权限的按钮
    </el-button>
    <br />
    <br />
    <el-divider content-position="left">
      all模式,路由以及view文件引入全部交给后端(权限复杂,且随时变更,建议使用此方案)
    </el-divider>
    <p>
      settings.js配置authentication为all即可完全交由后端控制,mock中有后端接口示例,权限繁琐,有几十种权限的项目直接用这种,
      由于演示环境是mock数据模拟,可能无法展现此功能的配置,只做如下展示,便于您的理解
    </p>
    <br />
    <el-row :gutter="20">
      <el-col :lg="12" :md="12" :sm="24" :xl="12" :xs="24">
        <el-table
          :data="tableData"
          :tree-props="{ children: 'children', hasChildren: 'hasChildren' }"
          border
          default-expand-all
          row-key="path"
        >
          <el-table-column
            label="name"
            prop="name"
            show-overflow-tooltip
          ></el-table-column>
          <el-table-column
            label="path"
            prop="path"
            show-overflow-tooltip
          ></el-table-column>
          <el-table-column
            label="component"
            prop="component"
            show-overflow-tooltip
          ></el-table-column>
          <el-table-column
            label="redirect"
            prop="redirect"
            show-overflow-tooltip
          ></el-table-column>
          <el-table-column
            label="标题"
            prop="meta.title"
            show-overflow-tooltip
          ></el-table-column>
          <el-table-column label="图标" show-overflow-tooltip>
            <template #default="{ row }">
              <span v-if="row.meta">
                <vab-icon
                  v-if="row.meta.icon"
                  :icon="['fas', row.meta.icon]"
                ></vab-icon>
              </span>
            </template>
          </el-table-column>
          <el-table-column label="是否固定" show-overflow-tooltip>
            <template #default="{ row }">
              <span v-if="row.meta">
                {{ row.meta.affix }}
              </span>
            </template>
          </el-table-column>
          <el-table-column label="是否无缓存" show-overflow-tooltip>
            <template #default="{ row }">
              <span v-if="row.meta">
                {{ row.meta.noKeepAlive }}
              </span>
            </template>
          </el-table-column>
          <el-table-column label="badge" show-overflow-tooltip>
            <template #default="{ row }">
              <span v-if="row.meta">
                {{ row.meta.badge }}
              </span>
            </template>
          </el-table-column>
        </el-table>
      </el-col>
    </el-row>
  </div>
</template>

<script>
  import { mapGetters } from 'vuex'
  import { tokenTableName } from '@/config'
  import { getRouterList } from '@/api/router'

  export default {
    name: 'Permissions',
    data() {
      return {
        form: {
          account: '',
        },
        tableData: [],
        res: [],
      }
    },
    computed: {
      ...mapGetters({
        username: 'user/username',
        permissions: 'user/permissions',
      }),
    },
    created() {
      this.fetchData()
    },
    mounted() {
      this.form.account = this.username
    },
    methods: {
      handleChangePermission() {
        localStorage.setItem(tokenTableName, `${this.form.account}-accessToken`)
        location.reload()
      },
      async fetchData() {
        const res = await getRouterList()
        this.tableData = res.data
        this.res = res
      },
    },
  }
</script>
