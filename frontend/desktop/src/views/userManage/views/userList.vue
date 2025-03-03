<template>
    <div id="userlist">
        <div id="tab">
            <div id="batch">
                <bk-dropdown-menu>
                    <div class="dropdown-trigger-btn" slot="dropdown-trigger">
                        <span>批量操作</span>
                        <i class="bk-icon icon-angle-down"></i>
                    </div>
                    <ul class="bk-dropdown-list" slot="dropdown-content">
                        <li>
                            <a href="javascript:;" @click="handleBatch('batchDel')"
                            >批量删除</a
                            >
                        </li>
                        <li>
                            <a href="javascript:;" @click="handleBatch('batchTag')"
                            >批量打tag</a
                            >
                        </li>
                    </ul>
                </bk-dropdown-menu>
                <bk-dialog
                    v-model="batch.dialogVisible"
                    theme="primary"
                    :mask-close="false"
                    title="确认删除以下用户？"
                    @confirm="handleBatchDel"
                >
                    <bk-tag-input
                        v-model="batch.checkedUsers"
                        :list="userlist"
                        :trigger="'focus'"
                        :allow-next-focus="false"
                        has-delete-icon
                        :clearable="false"
                        placeholder="请选择要删除的账号"
                    >
                    </bk-tag-input>
                </bk-dialog>
            </div>
            <div id="tabRight">
                <bk-input
                    left-icon="bk-icon icon-search"
                    placeholder="搜索用户"
                    v-model="search.name"
                    @blur="getUserlist()"
                    @keyup.enter.native="getUserlist()"
                    style="width: 300px"
                >
                </bk-input>
            </div>
        </div>
        <!-- 表格本体 -->
        <!--
        这个表格组件我感觉有点问题，配置height只能使用提供的属性，不可以直接配置css，属性又不支持calc()导致高度非常死板
        话说这个组件库好像比较喜欢写行内样式，连官方文档都经常写行内样式，我觉得逻辑和样式应该分开的
         -->
        <bk-table
            :data="userlist"
            :row-key="
                (row) => {
                    return row.id;
                }
            "
            :pagination="page"
            size="small"
            height="62vh"
            style="margin-top: 10px"
            auto-scroll-to-top
            @page-change="handlePageChange"
            @selection-change="handleSelect"
            @page-limit-change="handlePageLimitChange"
            @filter-change="handleFilterChange"
        >
            <!-- 表格空状态插槽 -->
            <template slot="empty">
                <bk-spin v-if="loadFlag" icon="circle-2-1"></bk-spin>
                <div v-else style="font-weight: 800; font-size: 18px">
                    无任何用户
                </div>
            </template>
            <bk-table-column type="selection" width="60" resizable></bk-table-column>
            <bk-table-column
                label="用户名"
                column-key="username"
                prop="username"
                width="150"
            ></bk-table-column>
            <bk-table-column
                label="姓名"
                column-key="name"
                prop="name"
                :width="100"
            ></bk-table-column>
            <bk-table-column
                label="性别"
                column-key="gender"
                prop="gender"
                :filters="filters.gender"
                :filter-multiple="false"
                :width="100"
            >
                <template slot-scope="props">{{
                    props.row.gender === "FEMALE"
                        ? "女"
                        : props.row.gender === "MALE"
                            ? "男"
                            : null
                }}</template>
            </bk-table-column>
            <bk-table-column
                label="邮箱"
                column-key="email"
                prop="email"
                align="center"
                width="180"
            ></bk-table-column>
            <bk-table-column
                label="标签"
                column-key="tag"
                prop="tag"
                width="200"
                align="center"
            >
                <template slot-scope="props">
                    <bk-tag
                        v-for="tag in props.row.tag"
                        :key="tag.tag_id"
                        :style="'background-color:' + colorTransform(tag.tag_color)"
                    >{{ tag.tag_value }}</bk-tag
                    >
                </template>
            </bk-table-column>
            <bk-table-column
                label="最近登陆时间"
                column-key="last_login"
                prop="last_login"
                :width="220"
                :filters="filters.min_date"
                :filter-multiple="false"
            ></bk-table-column>
            <bk-table-column label="用户操作" width="100">
                <template slot-scope="props">
                    <bk-button theme="primary" text @click="handleDelUser(props.row)"
                    >删除</bk-button
                    >
                    <bk-button theme="primary" text @click="handleEditUser(props.row)"
                    >修改</bk-button
                    >
                </template>
            </bk-table-column>
        </bk-table>
        <UserConfig
            title="修改用户"
            :config="userConfigCache"
            :visible.sync="editUserFlag"
        />
    </div>
</template>

<script>
    import {
        bkInput,
        bkTable,
        bkTableColumn,
        bkButton,
        bkTagInput,
        bkTag,
        bkDropdownMenu
    } from 'bk-magic-vue'
    import { colorTransform } from '@/common/util'
    const UserConfig = () => import('../components/userConfig')
    const BASE_UEL = 'api/users/'
    export default {
        components: {
            bkInput,
            bkTable,
            bkTableColumn,
            bkButton,
            UserConfig,
            bkTagInput,
            bkTag,
            bkDropdownMenu
        },
        data () {
            const current = new Date().getTime()
            return {
                // 搜索条件
                search: {
                    name: '',
                    gender: '',
                    min_date: '',
                    max_date: '',
                    role: ''
                },
                batch: {
                    // 批量操作选项
                    options: [
                        { id: 'batchDel', name: '批量删除' },
                        { id: 'batchTag', name: '批量打tag' }
                    ],
                    // 选中条目id
                    checkedUsers: [],
                    // 删除dialog
                    dialogVisible: false
                },
                // 用户列表
                userlist: [],
                // 新建用户
                importUserFlag: false,
                // 修改用户
                editUserFlag: false,
                userConfigCache: {},
                // 分页
                page: {
                    current: 1,
                    limit: 10,
                    count: 0,
                    limitList: [5, 10, 15, 20]
                },
                // 筛选
                filters: {
                    gender: [
                        { text: '男性', value: 'MALE' },
                        { text: '女性', value: 'FEMALE' }
                    ],
                    min_date: [
                        {
                            text: '近一星期',
                            value: new Date(current - 7 * 24 * 3600 * 1000).toLocaleString()
                        },
                        {
                            text: '近一月',
                            value: new Date(current - 30 * 24 * 3600 * 1000).toLocaleString()
                        },
                        {
                            text: '近一年',
                            value: new Date(current - 365 * 24 * 3600 * 1000).toLocaleString()
                        }
                    ]
                },
                // 加载状态
                loadFlag: true
            }
        },
        mounted () {
            this.getUserlist()
        },
        methods: {
            getUserlist () {
                return this.$http
                    .get(`${BASE_UEL}`, {
                        params: {
                            page: this.page.current,
                            page_size: this.page.limit,
                            gender: this.search.gender,
                            name: this.search.name,
                            min_date: this.search.last_login
                        }
                    })
                    .then((res) => {
                        this.page.count = res.data.count
                        this.userlist = res.data.results.map((item) => {
                            if (!item.last_login) {
                                return item
                            }
                            item.last_login = new Date(item.last_login).toLocaleString()
                            return item
                        })
                        this.loadFlag = false
                    })
            },
            deleteUser (idList) {
                return this.$http.post(`${BASE_UEL}batch/delete/`, {
                    id_list: idList
                })
            },
            handlePageLimitChange (limit) {
                this.page.limit = limit
                this.getUserlist()
            },
            handleBatchDel () {
                // 执行删除操作
                this.$bkInfo({
                    type: 'warning',
                    title: '确认批量删除(不可逆)？',
                    confirmLoading: true,
                    confirmFn: async () => {
                        try {
                            await new Promise((resolve) => {
                                this.deleteUser(this.batch.checkedUsers).then((res) => {
                                    resolve()
                                })
                            })
                            this.$bkMessage({
                                message: '删除成功',
                                theme: 'success'
                            })
                            return true
                        } catch (e) {
                            console.warn(e)
                            return false
                        } finally {
                            this.getUserlist()
                        }
                    }
                })
            },
            handlePageChange (page) {
                this.page.current = page
                this.getUserlist()
            },
            // 多选
            handleSelect (selection) {
                this.batch.checkedUsers = []
                selection.map((item) => {
                    this.batch.checkedUsers.push(item.id)
                })
            },
            // 删除单个
            handleDelUser (row) {
                this.$bkInfo({
                    title: '确认删除该用户？(不可逆)',
                    confirmLoading: true,
                    confirmFn: async () => {
                        try {
                            await new Promise((resolve, reject) => {
                                this.deleteUser([row.id]).then(
                                    (res) => {
                                        resolve()
                                    },
                                    () => {
                                        // eslint-disable-next-line prefer-promise-reject-errors
                                        reject()
                                    }
                                )
                            })
                            this.$bkMessage({
                                message: '删除成功',
                                theme: 'success'
                            })
                            return true
                        } catch (e) {
                            console.warn(e)
                            this.$bkMessage({
                                message: '删除失败',
                                theme: 'error'
                            })
                            return false
                        } finally {
                            this.getUserlist()
                        }
                    }
                })
            },
            handleEditUser (row) {
                this.editUserFlag = true
                this.userConfigCache = row
            },
            handleFilterChange (column) {
                for (const key in column) {
                    // 组件库支持多值条件，所以所有的条件value都是数组，这里转化一下
                    column[key] = column[key][0]
                }
                Object.assign(this.search, column)
                this.getUserlist()
            },
            handleBatch (opera) {
                if (opera === 'batchDel') {
                    if (this.batch.checkedUsers.length === 0) {
                        alert('请选择用户')
                        return
                    }
                    this.batch.dialogVisible = true
                } else if (opera === 'batchTag') {
                    alert('施工中...')
                }
            },
            colorTransform
        }
    }
</script>

<style lang="postcss">
#tab {
  display: flex;
  justify-content: space-between;
  margin-bottom: 5px;
  margin-top: 1px;
  #batch {
    display: flex;
    justify-content: space-between;
    .bk-dropdown-menu {
      padding: 8px 14px;
      background: #3a84ff;
      border-color: #3a84ff;
      color: #fff;
      span {
        font-size: 14px;
        font-weight: 800;
      }
    }
    .bk-select {
      width: 100px;
    }
  }
  #tabRight {
    display: flex;
    flex-direction: row;
  }
}
</style>
