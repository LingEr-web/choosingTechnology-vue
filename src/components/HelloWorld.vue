<template>
  <div class="hello">
    <!-- table2 -->
    <h3>编辑单元格，添加数据</h3>
    <div class="header">
      <el-button type="primary" size="mini" @click="add2" icon="el-icon-plus">新增</el-button>
    </div>
    <el-table border :data="tableData2" :header-cell-style="{background:'#409EFF',color:'#ffffff'}" highlight-current-row  @cell-dblclick="tableDbEdit2" style="width: 100%">
      <el-table-column label="序号" type="index" width="120"></el-table-column>

      <el-table-column label="编码" property="code">
        <template slot-scope="scope">
		      <el-input v-if="scope.row.code.edit && scope.row.explain.value!=='系统内置'"
		        ref="code"
		        v-model="scope.row.code.value"
		        style="width: 100%"
		        @blur="editBlur(scope.row,scope.row.code)">
		      </el-input>
		      <span v-else :class="{'grey': scope.row.explain.value =='系统内置'}">{{ scope.row.code.value }}</span>
		    </template>
      </el-table-column>

      <el-table-column property="name" label="名称">
        <template slot-scope="scope">
		      <el-input v-if="scope.row.name.edit && scope.row.explain.value!=='系统内置'"
		        ref="name"
		        v-model="scope.row.name.value"
		        style="width: 100%"
		        @blur="editBlur(scope.row,scope.row.name)">
		      </el-input>
		      <span v-else :class="{'grey': scope.row.explain.value =='系统内置'}">{{ scope.row.name.value }}</span>
		    </template>
      </el-table-column>

      <el-table-column label="默认">
        <template slot-scope="scope">
          <div>
            <el-radio :label="scope.row.id" v-model="templateRadio" @change.native="getTemplateRow2(scope.$index,scope.row)">&nbsp;</el-radio>
          </div>
        </template>
      </el-table-column>

      <el-table-column property="explain" label="说明">
        <template slot-scope="scope">
		      <el-input v-if="scope.row.explain.edit && scope.row.explain.value!=='系统内置'"
		        ref="explain"
		        v-model="scope.row.explain.value"
		        style="width: 100%"
		        @blur="editBlur(scope.row,scope.row.explain)">
		      </el-input>
		      <span v-else :class="{'grey': scope.row.explain.value =='系统内置'}">{{ scope.row.explain.value }}</span>
		    </template>
      </el-table-column>

      <el-table-column label="操作">
        <template slot-scope="scope">
          <el-button
            size="mini"
            type="danger"
            icon="el-icon-delete"
            :disabled = "scope.row.explain.value =='系统内置'"
            @click="del(scope.row.id)">删除</el-button>
        </template>
      </el-table-column>
    </el-table>
    <div class="table-pagination">
      <el-pagination
      background
      @size-change="handleSizeChange"
      @current-change="handleCurrentChange"
      :current-page="currentPage4"
      :page-sizes="[10,20,30]"
      :page-size="10"
      layout="total, sizes, prev, pager, next, jumper"
      :total="50">
      </el-pagination>
    </div>
    
    <!-- 树形 -->
    <div class="block">
      <div class="tree-btn">
        <el-button type="primary" size="mini" @click="addTreeNode(addTreeData)" icon="el-icon-plus"></el-button>
        <el-button type="danger" size="mini" @click="delTreeNode(delTreeData,addTreeData)" icon="el-icon-delete"></el-button>
        <el-button type="primary" size="mini" @click="upTreeNode(delTreeData,addTreeData)" icon="el-icon-top"></el-button>
        <el-button type="primary" size="mini" @click="downTreeNode(delTreeData,addTreeData)" icon="el-icon-bottom"></el-button>
      </div>
      <el-tree
        :data="treeData"
        node-key="id"
        :key="tree_key"
        ref="trees"
        :indent=15
        :default-expanded-keys="defaultExpand"
        highlight-current
        default-expand-all
        @node-click="treeClick"
        :expand-on-click-node="false">
        <div class="custom-tree-node" slot-scope="{ node, data }">
          <div 
            :class="{'custom-tree-node-text':(!data.label.codeedit)&&(!data.label.contedit)}" 
            v-if="(!data.label.codeedit)||(!data.label.contedit)" 
            @dblclick="editTree(data)" 
            >
            <span v-if="node.label.code && (!node.label.codeedit)">{{ node.label.code }}&nbsp;&nbsp;</span>
            <span v-if="node.label.cont && (!node.label.contedit)">{{ node.label.cont }}</span>
          </div>
          <el-input v-if="data.label.codeedit"
		        v-model="data.label.code"
            size="mini"
            style="margin-right:10px;"
            :ref="`treeCode${data.id}`"
		        @blur="editTreeBlur(node,data,'treeCode')">
		      </el-input>
          <el-input v-if="data.label.contedit"
		        v-model="data.label.cont"
            size="mini"
            style="margin-right:10px;"
            :ref="`treeCont${data.id}`"
		        @blur="editTreeBlur(node,data,'treeCont')">
		      </el-input>
        </div>
      </el-tree>
    </div>
  </div>
</template>
<script>
export default {
  name: 'HelloWorld',
  data () {
    return {
      templateRadio:'1',
      tableData2: [{
          id:'1',
          code: {value:'01',edit:false},
          name: {value:"公开",edit:false},
          explain: {value:"系统内置",edit:false}
        }, {
          id:'2',
          code: {value:'02',edit:false},
          name: {value:"内部",edit:false},
          explain: {value:"系统内置",edit:false}
        }, {
          id:'3',
          code: {value:'03',edit:false},
          name: {value:"秘密",edit:false},
          explain: {value:"系统内置",edit:false}
        }, {
          id:'4',
          code:{value:'04',edit:false},
          name: {value:"机密",edit:false},
          explain:{value:"系统内置",edit:false}
        },{
          id:'5',
          code:{value:'05',edit:false},
          name: {value:"绝密",edit:false},
          explain:{value:"系统内置",edit:false}
        },{
          id:'6',
          code:{value:'06',edit:false},
          name: {value:"非密",edit:false},
          explain:{value:"用户自定义",edit:false}
      }],
      tree_key: 0,
      defaultExpand:[],
      currentRow: null,
      addTreeData:null,
      delTreeData:null,
      downNodeStore:null,
      isTreeEdit:false,
      editTreeId:null,
      treeDataId:1000,
      treeData:  [
        {
        id: 1,
        label: {
          code:'',
          codeedit:false,
          contedit:false,
          cont:'DEMO01'
        },
        children: [
          {
            id: 3,
            label: {
              code:'A',
              cont:'系统差异码',
              codeedit:false,
              contedit:false,
            },
            children: [
              {
                id: 6,
                label: {
                  code:'A0',
                  cont:'牵引系统',
                  codeedit:false,
                  contedit:false,
                },
                children: [
                  {
                    id: 9,
                    label: {
                      code:'10',
                      cont:'动力分系统',
                      codeedit:false,
                      contedit:false,
                    },
                    children:[
                      {
                        id: 12,
                        label: {
                          code:'1000',
                          cont:'发动机单元',
                          codeedit:false,
                          contedit:false,
                        },
                        children:[
                          {
                            id:13,
                            label:{
                              code:'10',
                              cont:'邮箱分解',
                              codeedit:false,
                              contedit:false,
                            },
                            children:[
                              {
                                id:14,
                                label:{
                                  code:'A',
                                  cont:'油箱盖',
                                  codeedit:false,
                                  contedit:false,
                                }
                              }
                            ]
                          }
                        ]
                      }
                    ]
                  },
                  {
                    id: 10,
                    label: {
                      code:'11',
                      cont:'燃油系统',
                      codeedit:false,
                      contedit:false,
                    },
                  },
                  {
                    id: 11,
                    label: {
                      code:'12',
                      cont:'加热系统',
                      codeedit:false,
                      contedit:false,
                    },
                  },
                ]
              },
              
            ]
          },
          {
            id: 4,
            label: {
              code:'B',
              cont:'系统差异码',
              codeedit:false,
              contedit:false,
            },
            children: [
              {
                id: 7,
                label: {
                  code:'10',
                  cont:'',
                  codeedit:false,
                  contedit:false,
                },
              }
            ]
          },
          {
            id: 5,
            label: {
              code:'C',
              cont:'系统差异码',
              codeedit:false,
              contedit:false,
            },
            children: [
              {
                id: 8,
                label: {
                  code:'10',
                  cont:'',
                  codeedit:false,
                  contedit:false,
                },
              }
            ]
          }
        ]
        }, 
        {
          id: 2,
          label:  {
            code:'',
            cont:'DEMO02',
            codeedit:false,
            contedit:false,
          },
          children: [{
            id: 6,
            label:{
              code:'',
              cont:'enter text',
              codeedit:false,
              contedit:false,
            },
          }]
        }
      ],
      defaultProps: {
        children: 'children',
        label: 'label'
      },
      currentPage4:4
    }
  },
  methods: {
    // 分页
    handleSizeChange(){},
    handleCurrentChange(){},
    // 双击单元格可编辑
    /*tableDbEdit(row, column, cell, event){
      let _this = this;
      if (column.label != "单选") {
        let flag=true;
        let texts = event.target.innerText
        event.target.innerHTML = "";
        let cellInput = document.createElement("input");
        cellInput.value = texts;
        cellInput.setAttribute("type", "text");
        cellInput.style.width = "80%";
        cell.appendChild(cellInput);
        cellInput.focus()
        cellInput.onblur = function() {
          if(cellInput.value==""){
            _this.$notify.error({
              title: '',
              message: '内容不能为空！'
            });
            cellInput.focus()
          }else{
            _this.$notify.success({
              title: '',
              message: '修改成功！'
            });
            cell.removeChild(cellInput);
            event.target.innerHTML = cellInput.value;
            flag=true
          }
        };
      }
    },*/
    // 单选
    getTemplateRow2(index,row){
      console.log(index,row)
    },
    // 双击单元格可编辑
    tableDbEdit2(row, column, cell, event){
      let _this = this;
      if(row[column.property] && row.explain.value!=='系统内置'){
        row[column.property].edit=true
        setTimeout(()=>{
          _this.$refs[column.property].focus()
        })
      }
    },
    // 失去焦点验证提交
    editBlur(row,td){
      td.edit=false
      this.$notify.success({
        title: '',
        message: '修改成功！'
      });
    },
    // 添加
    add2(){
      this.tableData2.push({
        id: this.tableData2[this.tableData2.length-1].id+1,
        code: {value:'03',edit:false},
        name: {value:"秘密",edit:false},
        explain: {value:"用户自定义",edit:false}
      })
    },
    // 删除
    del(id){
      let _this=this; 
      _this.$confirm('此操作将永久删除该数据, 是否继续?', '提示', {
        confirmButtonText: '确定',
        cancelButtonText: '取消',
        type: 'warning'
      }).then(() => {
        let index='';
        for(let i=0;i<_this.tableData2.length;i++){
          if(_this.tableData2[i].id==id){
            index=i;
            break;
          }
        }
        _this.tableData2.splice(index,1)
        _this.$notify.success({
          title: '',
          message: '删除成功'
        });
      }).catch(() => {
        _this.$notify.success({
          title: '',
          message: '取消删除'
        });        
      });
    },
    // 树状
    // 选中
    treeClick(data,node){
      this.addTreeData=data;
      this.delTreeData=node;
      if(this.isTreeEdit){
        this.$refs.trees.setCurrentKey(this.editTreeId);
      }
    },
    // 添加
    addTreeNode(data){
      const newChild = { id: this.treeDataId++, label: {code:'00',cont:'未定义',codeedit:true,contedit:true,}, children: [] };
      if (!data.children) {
        this.$set(data, 'children', []);
      }
      data.children.push(newChild);
      this.editTree(newChild)
    },
    // 删除
    delTreeNode(node,data){
      const parent = node.parent;
      const children = parent.data.children || parent.data;
      const index = children.findIndex(d => d.id === data.id);
      children.splice(index, 1);
    },
    // 编辑
    editTree(data){
      data.label.codeedit=true;
      data.label.contedit=true;
      this.editTreeId=data.id;
      setTimeout(()=>{
        this.$refs.trees.setCurrentKey(data.id);
        this.$refs[`treeCode${data.id}`].focus();
      })
    },
    // 失去焦点提交
    editTreeBlur(node,data,events){
      if(events=='treeCode'){
        this.$notify.success({
          title: '',
          message: '修改成功！'
        });
        data.label.codeedit=false;
        this.isTreeEdit=true;
        setTimeout(()=>{
          this.$refs[`treeCont${data.id}`].focus()
        })
      }else if(events=='treeCont'){
        data.label.contedit=false;
        this.isTreeEdit=false;
        this.$notify.success({
          title: '',
          message: '修改成功！'
        });
      }
    },
    // 节点上移
    upTreeNode(node,data){
      const parent=node.parent;
      const children =parent.data.children || parent.data;
      const cIndex = children.findIndex(d=>d.id===data.id);
      if(parent.level===0 && cIndex===0){
        return;
      }else if(parent.level!==0 && cIndex===0){
        alert('不同父节点中移动')
      }else if((parent.level === 0 && cIndex !== 0) || (parent.level !== 0 && cIndex !== 0)){
        const tempChildrenNodex1 = children[cIndex - 1]
        const tempChildrenNodex2 = children[cIndex]
        this.$set(children, cIndex - 1, tempChildrenNodex2)
        this.$set(children, cIndex, tempChildrenNodex1)
        this.defaultExpand[0] = data.id;
        setTimeout(()=>{
          this.$refs.trees.setCurrentKey(data.id);
        })
      }
      this.tree_key++;
    },
    // 节点下移
    downTreeNode(node, data){
      const parent=node.parent;
      const children =parent.data.children || parent.data;
      const cIndex = children.findIndex(d=>d.id===data.id);
      const cLength = children.length - 1
      if(cIndex===cLength){
        alert('不能移动')
      }else if(cIndex===cLength){
        alert('不能移动')
      }else if(cIndex!==cLength){
        const tempChildrenNodex1 = children[cIndex + 1]
        const tempChildrenNodex2 = children[cIndex]
        this.$set(children, cIndex + 1, tempChildrenNodex2)
        this.$set(children, cIndex, tempChildrenNodex1)
        this.defaultExpand[0] = data.id;
        setTimeout(()=>{
          this.$refs.trees.setCurrentKey(data.id);
        })
      }
      this.tree_key++;
    }
  }
}
</script>
<style>
  .el-tree-node__content{
    height: 40px;
  }
  .el-tree--highlight-current .el-tree-node.is-current>.el-tree-node__content{
    background-color: #409EFF;
    color: #ffffff;
  }
</style>
<style scoped>
.pointer{
  cursor:pointer;
}
.header{
  width: 100%;
  display: flex;
  justify-content: flex-start;
  margin-bottom: 10px;
}
.table-headers{
  background-color: #409EFF;
}
.grey{
  color: #aeb0b3;
}
.table-pagination{
  width: 100%;
  height: 60px;
  display: flex;
  justify-content: flex-end;
  align-items: center;
}
.tree-btn{
  width: 100%;
  height: 100px;
  display: flex;
  justify-content: flex-start;
  align-items: center;
}
.custom-tree-node{
  height: 100%;
  width: 100%;
  display: flex;
  justify-content: flex-start;
  align-items: center;
}
.custom-tree-node-text{
  height: 100%;
  width: 100%;
  display: flex;
  justify-content: flex-start;
  align-items: center;
}
</style>