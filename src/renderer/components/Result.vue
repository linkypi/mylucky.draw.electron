<template>
  <el-dialog
    :visible="visible"
    @close="$emit('update:visible', false)"
    width="750px"
    class="c-Result"
    :append-to-body="true"
  >
    <div class="dialog-title" style="text-align: center;" slot="title">
      <span :style="{ fontSize: '32px' ,color: '#e0c819',fontWeight: 'bold'}">
        抽奖结果
      </span>
      <!-- <span :style="{ fontSize: '14px', color: '#999', marginLeft: '10px' }">
        (点击号码可以删除)
      </span> -->
    </div>
    <div style="overflow-y: scroll;height: 600px;" class="dialog-body">
      <!-- <div
        v-for="(item, index) in resultList"
        :key="index"
        class="listrow"
        @click="
          event => {
            deleteRes(event, item);
          }
        "
      >
        <span class="name">
          {{ item.name }}
        </span>
        <span class="value">
          <span v-if="item.value && item.value.length === 0">
            暂未抽奖
          </span>
          <span
            class="card"
            v-for="(data, j) in item.value"
            :key="j"
            :data-res="data"
          >
            {{ data }}
          </span>
        </span>
      </div> -->


      <el-collapse v-for="(item, index) in this.getResult()"
        :key="index"
        class="listrow"
        @click="
          event => {
            deleteRes(event, item);
          }
        ">
        <el-collapse-item style="width:100%" class="is-active">
          <template slot="title">
              <span class="name">
                {{ item.name }}
              </span>
          </template>
          <span class="value">
            <span v-if="item.value && item.value.length === 0">
              暂未抽奖
            </span>
            <span
              class="card"
              v-for="(data, j) in item.value"
              :key="j"
              :data-res="data"
            >
              {{ data }}
            </span>
          </span>
        </el-collapse-item>
      </el-collapse>
    </div>
  </el-dialog>
</template>
<script>
import { conversionCategoryName, getDomData } from '@/helper/index';
export default {
  name: 'c-Result',
  props: {
    visible: Boolean
  },
  computed: {
    result: {
      get() {
          //  console.log(state);
        var ret = {};
        var list = this.$store.state.list;
        for(var item in this.$store.state.result){
          var arr = [];
          this.$store.state.result[item].forEach((xx)=>{
            for(var index=0;index<list.length;index++){
              if(list[index].key == xx){
                arr.push(list[index].name)
              }
            }
          })
          ret[item] = arr;
        }
        return ret;
      },
      set(val) {
        this.$store.commit('setResult', val);
      }
    },
    resultList() {
      const list = [];
      for (const key in this.result) {
        if (this.result.hasOwnProperty(key)) {
          const element = this.result[key];
          let name = conversionCategoryName(key);
          list.push({
            label: key,
            name,
            value: element
          });
        }
      }
      return list;
    }
  },
  methods: {
    getResult(){
      var ret = {};
      // debugger
      var list = this.$store.state.list;
      for(var item in this.$store.state.result){
        var arr = [];
        this.$store.state.result[item].forEach((xx)=>{
          for(var index=0;index<list.length;index++){
            if(list[index].key == xx){
              arr.push(list[index].name)
            }
          }
        })
        ret[item] = arr;
      }
    
      const arrs = [];
      for (const key in ret) {
        if (ret.hasOwnProperty(key)) {
          const element = ret[key];
          let name = conversionCategoryName(key);
          arrs.push({
            label: key,
            name,
            value: element
          });
        }
      }
      return arrs;
    },
    deleteRes(event, row) {
      const Index = getDomData(event.target, 'res');
      if (!Index) {
        return;
      }
      this.$confirm('此操作将移除该中奖号码，确认删除?', '警告', {
        confirmButtonText: '确定',
        cancelButtonText: '取消',
        type: 'warning'
      })
        .then(() => {
          if (Index) {
            const result = this.result;
            result[row.label] = this.result[row.label].filter(
              item => item !== Number(Index)
            );
            this.result = result;
            this.$message({
              type: 'success',
              message: '删除成功!'
            });
          }
        })
        .catch(() => {
          this.$message({
            type: 'info',
            message: '已取消'
          });
        });
    }
  }
};
</script>
<style lang="scss">
.c-Result {
  .listrow {
    display: flex;
    line-height: 30px;
    .name {
      text-align: center;
      width: 100%;
      color: #ffeb00;
      color: #ecb518;
      font-weight: bold;
      font-size: 20px;
    }
    .value {
      flex: 1;
    }
    .card {
      display: inline-block;
      width: 90px;
      height: 40px;
      line-height: 40px;
      text-align: center;
      font-size: 18px;
      font-weight: bold;
      border-radius: 4px;
      border: 1px solid #ccc;
      color: #f7b133;
      background-color: #a23a3a;
      margin-left: 5px;
      margin-bottom: 5px;
      position: relative;
      cursor: pointer;
      &:hover {
        &::before {
          content: '删除';
          width: 100%;
          height: 100%;
          background-color: #ccc;
          position: absolute;
          left: 0;
          top: 0;
          color: red;
        }
      }
    }
  }
}
</style>
