<template>
    <div v-loading="loading">
        <h1>员工管理</h1>
        <p style="color: red;font-size: 24px;" align="left">条件筛选</p>
        <div align="left">
            身份证号
            <input
                type="text"
                placeholder="包含关键字"
                id="idSearch"
                v-model="idSearch"
                class="input"
                required="false"
                style=" width:220px;
              font-family: 'Fira Code', '汉仪南宫体简';
            "
            />&emsp;姓名
            <input
                type="text"
                placeholder="包含关键字"
                id="nameSearch"
                v-model="nameSearch"
                class="input"
                required="false"
                style=" width:220px;
              font-family: 'Fira Code', '汉仪南宫体简';
            "
            />&emsp;所属支行
            <input
                type="text"
                placeholder="包含关键字"
                id="bankSearch"
                class="input"
                v-model="bankSearch"
                required="false"
                style=" width:220px;
              font-family: 'Fira Code', '汉仪南宫体简';
            "
            />&emsp;所在部门
            <input
                type="text"
                placeholder="包含关键字"
                id="deptSearch"
                class="input"
                v-model="deptSearch"
                required="false"
                style=" width:220px;
              font-family: 'Fira Code', '汉仪南宫体简';
            "
            />&emsp;<br /><br />电话号码
            <input
                type="text"
                placeholder="包含关键字"
                id="telSearch"
                v-model="telSearch"
                class="input"
                required="false"
                style=" width:180px;
              font-family: 'Fira Code', '汉仪南宫体简';
            "
            />&emsp;家庭住址
            <input
                type="text"
                placeholder="包含关键字"
                id="addrSearch"
                v-model="addrSearch"
                class="input"
                required="false"
                style=" width:220px;
              font-family: 'Fira Code', '汉仪南宫体简';
            "
            />&emsp;入职日期
            <input
                type="date"
                placeholder="下界"
                id="lowerBound"
                v-model="lowerBound"
                class="input"
                required="false"
                style="   width:140px;
                    font-family: 'Fira Code', '汉仪南宫体简';
                "
            />~~
            <input
                type="date"
                placeholder="上界"
                class="input"
                id="upperBound"
                v-model="upperBound"
                required="false"
                style=" width:140px;
              font-family: 'Fira Code', '汉仪南宫体简';
            "
            />&emsp;
            <el-button size="small" type="primary" @click="submit()">查询</el-button>
            <el-button size="small" type="primary" @click="reset()">重置</el-button>
        </div>
        <br />
        <p style="color: red;font-size: 24px;" align="left">员工信息表</p>
        <div align="left">
            <el-button type="success" size="small" @click="insertEvent('elxEditable1')" v-if="permission == 'SUB_BANK'">新增</el-button>
            <el-button type="success" size="small" @click="exportCsvEvent('elxEditable1')">导出</el-button>
        </div>
        <br />
        <elx-editable
            ref="elxEditable1"
            class="table"
            border
            :data.sync="list"
            :edit-config="{ trigger: 'manual', mode: 'row', clearActiveMethod: clearActiveMethod1 }"
            style="width: 100%"
        >
            <elx-editable-column type="index" width="55"></elx-editable-column>
            <elx-editable-column prop="id" label="身份证号" min-width="100" :edit-render="{ name: 'ElInput' }"></elx-editable-column>
            <elx-editable-column prop="name" label="姓名" :edit-render="{ name: 'ElInput' }"></elx-editable-column>
            <elx-editable-column prop="bank" label="所属支行" :edit-render="{ name: 'ElInput' }"></elx-editable-column>
            <elx-editable-column prop="dept" label="所在部门" :edit-render="{ name: 'ElInput' }"></elx-editable-column>
            <elx-editable-column prop="tel" label="电话号码" :edit-render="{ name: 'ElInput' }"></elx-editable-column>
            <elx-editable-column prop="addr" label="家庭住址" :edit-render="{ name: 'ElInput' }"></elx-editable-column>
            <elx-editable-column
                prop="date_s"
                label="入职日期"
                :edit-render="{
                    name: 'ElDatePicker',
                    props: { type: 'date', format: 'yyyy-MM-dd' }
                }"
            ></elx-editable-column>
            <elx-editable-column label="操作" width="250">
                <template v-slot="scope">
                    <template v-if="$refs.elxEditable1.hasActiveRow(scope.row)">
                        <el-button size="small" type="success" @click="saveRowEvent('elxEditable1', scope.row)">保存</el-button>
                        <el-button size="small" type="warning" @click="cancelRowEvent('elxEditable1', scope.row)">取消</el-button>
                    </template>
                    <template v-else>
                        <el-button size="small" type="primary" @click="openActiveRowEvent('elxEditable1', scope.row)">编辑</el-button>
                        <el-button size="small" type="danger" @click="removeEvent('elxEditable1', scope.row)" v-if="permission == 'SUB_BANK'">删除</el-button>
                        <el-button size="small" type="success" @click="showDetail(scope.row)">详情</el-button>
                    </template>
                </template>
            </elx-editable-column>
        </elx-editable>
        <div v-if="showlink">
            <br />
            <p style="color: red;font-size: 24px;" align="left">
                客户联系表
                <el-button type="success" size="small" @click="showlink = false">关闭</el-button>
            </p>
            <div align="left">
                <el-button type="success" size="small" @click="insertEvent('elxEditable2')">新增</el-button>
                <el-button type="success" size="small" @click="exportCsvEvent('elxEditable2')">导出</el-button>
            </div>
            <br />
            <elx-editable
                ref="elxEditable2"
                class="table"
                border
                :data.sync="linklist"
                :edit-config="{ trigger: 'manual', mode: 'row', clearActiveMethod: clearActiveMethod2 }"
                style="width: 100%"
            >
                <elx-editable-column type="index" width="55"></elx-editable-column>
                <elx-editable-column prop="staffid" label="员工身份证号"></elx-editable-column>
                <elx-editable-column prop="staffname" label="员工姓名"></elx-editable-column>
                <elx-editable-column prop="id" label="客户身份证号" :edit-render="{ name: 'ElInput' }"></elx-editable-column>
                <elx-editable-column prop="name" label="客户姓名"></elx-editable-column>
                <!-- <elx-editable-column prop="type" label="与客户关系" :edit-render="{ name: 'ElSelect', options: serviceList }"></elx-editable-column> -->
                <elx-editable-column prop="type" label="与客户关系" :edit-render="{ name: 'ElInput' }"></elx-editable-column>
                <elx-editable-column label="操作" width="160">
                    <template v-slot="newscope">
                        <template v-if="$refs.elxEditable2.hasActiveRow(newscope.row)">
                            <el-button size="small" type="success" @click="saveRowEvent('elxEditable2', newscope.row)">保存</el-button>
                            <el-button size="small" type="warning" @click="cancelRowEvent('elxEditable2', newscope.row)">取消</el-button>
                        </template>
                        <template v-else>
                            <el-button size="small" type="primary" @click="openActiveRowEvent('elxEditable2', newscope.row)">编辑</el-button>
                            <el-button size="small" type="danger" @click="removeEvent('elxEditable2', newscope.row)">删除</el-button>
                        </template>
                    </template>
                </elx-editable-column>
            </elx-editable>
        </div>
    </div>
</template>

<script>
import XEUtils from "xe-utils";
import XEAjax from "xe-ajax";
import { MessageBox, Message } from "element-ui";
export default {
    data() {
        return {
            loading: false,
            list: [],
            linklist: [],
            serviceList: [
                {
                    label: "银行账户负责人",
                    value: "0"
                },
                {
                    label: "贷款负责人",
                    value: "1"
                }
            ],
            isClearActiveFlag: true,
            nameSearch: "",
            idSearch: "",
            bankSearch: "",
            deptSearch: "",
            telSearch: "",
            addrSearch: "",
            lowerBound: "",
            upperBound: "",
            showlink: false,
            detail: "",
            detailName: "",
            permission: "",
            primary: null //全局变量，保存记录修改前的主键。当没有活跃的记录时为null，当新增记录时也为null
        };
    },
    created() {
        this.permission = localStorage.getItem("type");
        if (this.permission != "SUB_BANK" && this.permission != "EMPLOYEE") {
            this.$router.push("/404");
        }
        this.findList();
    },
    methods: {
        findList() {
            this.loading = true;
            this.list = [
                // {
                //     id: "33122220001010001X",
                //     name: "张三",
                //     bank: "合肥城南支行",
                //     dept: "人事处",
                //     tel: "13822100086",
                //     addr: "合肥浣纱路256号",
                //     date: new Date("2018-2-2")
                // }
            ];
            this.linklist = [
                // {
                //     id: "33122212121210001X",
                //     name: "吴邪",
                //     staffid: "33122220001010001X",
                //     staffName: "张三",
                //     type: "1"
                // }
            ];
            this.loading = false;
        },
        formatterDate(row, column, cellValue, index) {
            return XEUtils.toDateString(cellValue, "yyyy-MM-dd");
        },
        clearActiveMethod1({ type, row, rowIndex }) {
            return this.isClearActiveFlag && type === "out" ? this.checkSaveData("elxEditable1", row) : this.isClearActiveFlag;
        },
        clearActiveMethod2({ type, row, rowIndex }) {
            return this.isClearActiveFlag && type === "out" ? this.checkSaveData("elxEditable2", row) : this.isClearActiveFlag;
        },
        //新增记录
        insertEvent(name) {
            let activeInfo = this.$refs[name].getActiveRow();
            if (!activeInfo) {
                if (name === "elxEditable1") {
                    this.$refs[name].insert().then(({ row }) => {
                        this.$refs[name].setActiveRow(row);
                    });
                } else {
                    if (this.permission != "SUB_BANK" && this.detail.id != localStorage.getItem("username")) {
                        Message({ message: "您没有操作权限", type: "warning" });
                        return;
                    }
                    this.$refs[name].insert({ staffid: this.detail.id, staffname: this.detail.name }).then(({ row }) => {
                        this.$refs[name].setActiveRow(row);
                    });
                }
            }
        },
        customExportCsvEvent(name, opts) {
            this.$refs[name].exportCsv(opts);
        },

        //提交查询请求
        submit() {
            this.showlink = false;
            this.$http
                .post(
                    "http://" + document.domain + ":5000/staff",
                    {
                        type: "Search",
                        nameSearch: this.nameSearch,
                        idSearch: this.idSearch,
                        bankSearch: this.bankSearch,
                        deptSearch: this.deptSearch,
                        telSearch: this.telSearch,
                        addrSearch: this.addrSearch,
                        lowerBound: XEUtils.toDateString(this.lowerBound, "yyyy-MM-dd"),
                        upperBound: XEUtils.toDateString(this.upperBound, "yyyy-MM-dd")
                    },
                    {
                        emulateJSON: true
                    }
                )
                .then(function(response) {
                    if (parseInt(response.body.code) === 200) {
                        this.list = response.body.list;
                        Message({ message: "查询成功", type: "success" });
                    } else {
                        Message({ message: "查询失败", type: "warning" });
                    }
                });
        },
        //清空查询栏
        reset() {
            this.nameSearch = "";
            this.idSearch = "";
            this.deptSearch = "";
            this.telSearch = "";
            this.addrSearch = "";
            this.lowerBound = "";
            this.upperBound = "";
        },
        showDetail(row) {
            this.showlink = true;
            this.detail = row;
            this.$http
                .post(
                    "http://" + document.domain + ":5000/staffCustomer",
                    {
                        type: "SearchByStaff",
                        staffid: row.id
                    },
                    {
                        emulateJSON: true
                    }
                )
                .then(function(response) {
                    if (parseInt(response.body.code) === 200) {
                        this.linklist = response.body.list;
                        for (var i = 0; i < this.linklist.length; i++) {
                            this.linklist[i].staffname = row.name;
                            this.linklist[i].staffid = row.id;
                        }
                        Message({ message: "查询成功", type: "success" });
                    } else {
                        this.linklist=[];
                        Message({ message: "没有查到任何记录", type: "warning" });
                    }
                });
        },

        // 判断多表格切换时是否有数据没有保存
        checkSaveData(name, row) {
            if (this.$refs[name].hasRowChange(row)) {
                this.isClearActiveFlag = false;
                MessageBox.confirm("您离开了表格，检测未保存的内容，是否在离开前保存修改?", "温馨提示", {
                    closeOnClickModal: false,
                    distinguishCancelAndClose: true,
                    confirmButtonText: "保存",
                    cancelButtonText: "放弃修改",
                    type: "warning"
                })
                    .then(() => {
                        this.saveRowEvent(name, row);
                    })
                    .catch(action => {
                        if (action === "cancel") {
                            this.$refs[name].revert(row);
                            this.$refs[name].clearActive();
                            if (this.primary == null) {
                                this.$refs[name].remove(row);
                            }
                            this.primary = null;
                            Message({ message: "放弃修改并离开当前行", type: "warning" });
                        } else {
                            this.$refs[name].setActiveRow(row);
                            Message({ message: "停留在当前行编辑", type: "info" });
                        }
                    })
                    .then(() => {
                        this.isClearActiveFlag = true;
                    });
                return false;
            } else {
                this.primary = null;
            }
            return this.isClearActiveFlag;
        },
        openActiveRowEvent(name, row) {
            switch (name) {
                case "elxEditable1":
                    if (this.permission != "SUB_BANK" && row.id != localStorage.getItem("username")) {
                        Message({ message: "您没有操作权限", type: "warning" });
                        return;
                    }
                    break;
                case "elxEditable2":
                    if (this.permission != "SUB_BANK" && row.staffid != localStorage.getItem("username")) {
                        Message({ message: "您没有操作权限", type: "warning" });
                        return;
                    }
                    break;
            }

            this.$nextTick(() => {
                let activeInfo = this.$refs[name].getActiveRow();
                // 如果当前行正在编辑中，禁止编辑其他行
                if (activeInfo) {
                    if (activeInfo.row === row || !this.$refs[name].checkValid().error) {
                        if (activeInfo.isUpdate) {
                            this.isClearActiveFlag = false;
                            MessageBox.confirm("检测到未保存的内容，是否在离开前保存修改?", "温馨提示", {
                                closeOnClickModal: false,
                                distinguishCancelAndClose: true,
                                confirmButtonText: "保存",
                                cancelButtonText: "放弃修改",
                                type: "warning"
                            })
                                .then(() => {
                                    this.$refs[name].setActiveRow(row);
                                    this.primary = row.id;
                                    console.log(row.id);
                                    this.saveRowEvent(name, activeInfo.row);
                                })
                                .catch(action => {
                                    if (action === "cancel") {
                                        this.$refs[name].revert(activeInfo.row);
                                        this.$refs[name].setActiveRow(row);
                                        Message({ message: "放弃修改并离开当前行", type: "warning" });
                                    } else {
                                        Message({ message: "停留在当前行编辑", type: "info" });
                                    }
                                })
                                .then(() => {
                                    this.isClearActiveFlag = true;
                                });
                        } else {
                            this.$refs[name].setActiveRow(row);
                            this.primary = row.id;
                            console.log(row.id);
                        }
                    }
                } else {
                    this.$refs[name].setActiveRow(row);
                    this.primary = row.id;
                    console.log(row.id);
                }
            });
        },
        removeEvent(name, row) {
            if (this.permission != "SUB_BANK" && row.staffid != localStorage.getItem("username")) {
                Message({ message: "您没有操作权限", type: "warning" });
                return;
            }
            switch (name) {
                case "elxEditable1":
                    this.$http
                        .post(
                            "http://" + document.domain + ":5000/staff",
                            {
                                type: "Delete",
                                primary: row.id
                            },
                            {
                                emulateJSON: true
                            }
                        )
                        .then(function(response) {
                            if (parseInt(response.body.code) === 200) {
                                this.$refs.elxEditable1.remove(row);
                                Message({ message: "删除成功", type: "success" });
                            } else {
                                Message({ message: "删除失败，有关联客户信息", type: "warning" });
                            }
                        });
                    break;
                case "elxEditable2":
                    this.userLoading = true;
                    this.$http
                        .post(
                            "http://" + document.domain + ":5000/staffCustomer",
                            {
                                type: "Delete",
                                custid: row.id,
                                staffid: row.staffid
                            },
                            {
                                emulateJSON: true
                            }
                        )
                        .then(function(response) {
                            if (parseInt(response.body.code) === 200) {
                                this.$refs.elxEditable2.remove(row);
                                Message({ message: "删除成功", type: "success" });
                            } else {
                                Message({ message: "删除失败", type: "warning" });
                            }
                        });
                    break;
            }
        },
        saveRowEvent(name, row) {
            switch (name) {
                case "elxEditable2":
                    if (row.staffid == null || row.staffid == "") {
                        Message({ message: "字段不能为空", type: "warning" });
                        return;
                    }
                case "elxEditable1":
                    if (row.id == null || row.id == "") {
                        Message({ message: "字段不能为空", type: "warning" });
                        return;
                    }
            }
            console.log(row);
            this.$refs[name].validateRow(row, valid => {
                if (valid && this.$refs[name].hasRowChange(row)) {
                    switch (name) {
                        case "elxEditable1":
                            this.$http
                                .post(
                                    "http://" + document.domain + ":5000/staff",
                                    {
                                        type: "Update",
                                        id: row.id,
                                        name: row.name,
                                        bank: row.bank,
                                        dept: row.dept,
                                        tel: row.tel,
                                        addr: row.addr,
                                        date_s: XEUtils.toDateString(row.date_s, "yyyy-MM-dd"),
                                        old_primary: this.primary //null代表新增
                                    },
                                    {
                                        emulateJSON: true
                                    }
                                )
                                .then(function(response) {
                                    if (parseInt(response.body.code) === 200) {
                                        //更新合法
                                        this.primary = null;
                                        this.$refs.elxEditable1.clearActive();
                                        Message({ message: "保存成功", type: "success" });
                                        this.$refs.elxEditable1.reloadRow(row);
                                    } else if (parseInt(response.body.code) === 400) {
                                        Message({ message: "新增记录失败，可能是身份证号重复", type: "warning" });
                                    } else {
                                        Message({ message: "保存失败\n" + response.body.msg, type: "warning" });
                                    }
                                });
                            break;
                        case "elxEditable2":
                            this.$http
                                .post(
                                    "http://" + document.domain + ":5000/staffCustomer",
                                    {
                                        type: "Update",
                                        custID: row.id,
                                        staffID: row.staffid, //该字段是不变的
                                        serviceType: row.type,
                                        old_custID: this.primary, //null代表新增，这是旧的客户身份证号
                                        old_staffID: row.staffid
                                    },
                                    {
                                        emulateJSON: true
                                    }
                                )
                                .then(function(response) {
                                    if (parseInt(response.body.code) === 200) {
                                        //更新合法
                                        this.primary = null;
                                        this.$refs.elxEditable2.clearActive();
                                        row.name = response.body.record.name;
                                        row.staffname = response.body.record.staffname;
                                        this.$refs.elxEditable2.reloadRow(row);
                                        this.showDetail(this.detail);
                                        Message({ message: "保存成功", type: "success" });
                                    } else {
                                        Message({ message: "保存失败", type: "warning" });
                                    }
                                });
                            break;
                    }
                } else if (valid) {
                    this.$refs[name].clearActive();
                    this.primary = null;
                }
            });
        },
        cancelRowEvent(name, row) {
            let activeInfo = this.$refs[name].getActiveRow();
            if (activeInfo && activeInfo.isUpdate) {
                this.isClearActiveFlag = false;
                MessageBox.confirm("检测到未保存的内容，确定放弃修改?", "温馨提示", {
                    closeOnClickModal: false,
                    confirmButtonText: "放弃更改",
                    cancelButtonText: "返回",
                    type: "warning"
                })
                    .then(action => {
                        if (action === "confirm") {
                            this.$refs[name].clearActive();
                            this.$refs[name].revert(row);
                            if (this.primary == null) {
                                this.$refs[name].remove(row);
                            }
                            this.primary = null;
                        } else {
                            this.$refs[name].setActiveRow(row);
                        }
                    })
                    .catch(e => e)
                    .then(() => {
                        this.isClearActiveFlag = true;
                    });
            } else {
                this.$refs[name].clearActive();
                this.primary = null;
            }
        }
    }
};
</script>

<style>
.input{
    outline-style: none ;
    border: 1px solid #ccc; 
    border-radius: 6px;
    padding: 8px 14px;
    width: 620px;
    font-size: 14px;
    font-weight: 700;
    font-family: "Fira Code", "汉仪南宫体简";
}
.input:focus{
    border-color: #66afe9;
    outline: 0;
    -webkit-box-shadow: inset 0 3px 3px rgba(0,0,0,.075),0 0 8px rgba(102,175,233,.6);
    box-shadow: inset 0 3px 3px rgba(0,0,0,.075),0 0 8px rgba(102,175,233,.6)
}
</style>
