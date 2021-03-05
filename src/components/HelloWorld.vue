<template>
  <div class="box-wrapper">
    <div v-if="showAdd">
      <button @click="() => addHandle()">添加</button>
    </div>
    <div v-else>
      <div
        v-for="(item, _index) in copySelectData"
        :key="_index"
      >
        <div class="list-item" v-if="item.list.length">
          <div class="title">{{item.title}}</div>
          <div
            v-for="(list, index) in item.list"
            :key="list"
            class="list-item-item"
          >
            <span>{{list}}</span>
            <div class="list-item-item-btn">
              <span @click="() => addHandle(item, index)">添加</span>
              <span @click="() => deleteHandle(item.id, index)">删除</span>
            </div>
          </div>
        </div>
      </div>
    </div>
    <el-dialog
      :visible="visible"
      width="300px"
      :show-close="false"
    >
      <el-select v-model="value">
        <el-option
          v-for="item in selectData"
          :key="item.id"
          :label="item.title"
          :value="item.title"
        ></el-option>
      </el-select>

      <template slot="footer">
        <el-button @click="saveHandle">保存</el-button>
      </template>
    </el-dialog>

  </div>
</template>

<script>
import Mock from 'mockjs'
export default {
  name: 'HelloWorld',
  data () {
    return {
      visible: false,
      showAdd: true,
      selectData: [], // 下拉数据
      copySelectData: [], // 列表数据
      value: '',
      addBoxId: null,
      addListIndex: null,
    }
  },
  mounted() {
    this.mockDataHandle()
  },
  methods: {
    mockDataHandle() {
      const str = 'A'
      for(let i = 0; i < 4; i++) {
        const title = String.fromCharCode(str.charCodeAt() + i)
        const obj = {
          id: Mock.mock('@id'),
          title,
          list: []
        }
        this.selectData.push(obj)
      }
      this.copySelectData = JSON.parse(JSON.stringify(this.selectData))
    },
    // 
    addHandle(item, index) {
      if (item) {
        this.addBoxId = item.id
        this.value = item.title
      }
      if (index || index === 0) {
        this.addListIndex = index
      }
      this.visible = true
    },
    saveHandle() {
      const that = this
      if (that.addBoxId) {
        that.copySelectData.forEach(item => {
          if (item.id === that.addBoxId) {
            if (item.title === that.value) {
              item.list.push(`第${item.list.length + 1}`)
            } else {
              const newArr = item.list.slice(that.addListIndex + 1, item.list.length)
              item.list.length = that.addListIndex + 1

              that.copySelectData.forEach(k => {
                if (k.title === that.value) {
                  k.list.push(`第${k.list.length + 1}`)
                }
              })

              if (that.copySelectData.every(k => k.id !== item.id + '-1')) {
                const obj = {
                  id: `${item.id}-1`,
                  title: item.title,
                  list: newArr,
                }

                that.copySelectData.push(obj)
              } else {
                that.copySelectData.forEach(k => {
                  if (k.id === item.id + '-1') {
                    k.list.concat(newArr)
                  }
                })
              }
            }
          }
        })
      } else {
        that.copySelectData.forEach(item => {
          if (item.title === that.value) {
            item.list.push(`第${item.list.length + 1}`)
            that.showAdd = false
          }
        })
      }
      
      that.visible = false
    },

    deleteHandle(id, index) {
      this.copySelectData = this.copySelectData.map(item => {
        if (item.id === id) {
          item.list = item.list.filter((k, _index) => _index !== index)
        }
        return item
      })
    }
  }
}
</script>

<!-- Add "scoped" attribute to limit CSS to this component only -->
<style scoped>
.box-wrapper {
  text-align: center;
}
.list-item {
  border: 1px solid red;
  padding: 10px;
  margin: 10px 0;
}

.list-item-item {
  display: flex;
  justify-content: space-between;
  background: #eee;
  margin: 5px 0;
  padding: 10px;
}

.list-item-item-btn {
  color: red;
}

.list-item-item-btn span {
  cursor: pointer;
  margin-left: 5px;
}
</style>
