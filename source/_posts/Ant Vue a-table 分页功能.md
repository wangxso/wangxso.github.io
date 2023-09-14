title: Ant Vue a-table 分页功能
date: 2021-02-06 08:26:00
categories: zczb
tags: []
---
要是用分页功能自定义配置，你需要创建一个pagination的配置对象
```js
data() {
            return{
                pagination:{
                    current: 1,
                    pageSize: 10,
                    pageSizeOptions: ['10', '20', '30'],
                    showTotal: (total, range) => {
                        return range[0] + '-' +range[1] + '共' + total + '条';
                    },
                    showQuickJumper: true,
                    showSizeChanger: true,
                    total: 0,
                }
            }
        },
```
然后table是这样的
```js
<a-table
                :loading="loading"
                :columns="columns"
                :data-source="problemList"
                :pagination="pagination"
                @change="handleTableChange">
            <template slot="operation" slot-scope="text, record">
            <div class="editable-row-operations">
                <a  @click="() => toEdit(record)">Edit</a>
            </div>
            </template>
        </a-table>
```
有个方法是这样的
```js
handleTableChange(pagination) {
                this.pagination.current = pagination.current
                this.pagination.pageSize = pagination.pageSize

            },
```