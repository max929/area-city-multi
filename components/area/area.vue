<template>
  <div class="area clearfix">
    <div class="region-wrapper" v-for="(province,index) in citydata" :key="index">
      <Poptip placement="right-start" width="250" trigger="hover">
        <div class="provice-wrapper">
          <Checkbox :value="province.checkAll" @click.prevent.native="handleCheckProvince(province)">{{province.label}}</Checkbox>
        </div>
        <div class="city-wrapper" slot="content">
          <Checkbox class="city-item" v-for="(city,childIndex) in province.children" :key="childIndex" :value="city.isCheck" @click.prevent.native="handleCheckCity(province,city)">{{city.label}}</Checkbox>
        </div>
      </Poptip>
      <span class="select-count" v-if="province.childrenCount !== province.isCheckChildrenCount">{{province.isCheckChildrenCount}} / {{province.childrenCount}}</span>
    </div>
  </div>
</template>

<script>
module.exports = {
    props: {
			areadata: Array
		},
    data: function() {
        return {
          citydata: []
        };
    },
    watch:{
        'areadata': {
            deep: true,
            handler: function (newVal,oldVal){
                // code
                if (newVal){
                  this.areadata=newVal;
                  this.setDataVo();
                }
            }
        }
    },
    mounted() {
          this.citydata = get2LevelArea();
        },
    methods: {
      setDataVo() {
        if (this.areadata.length !== 0 && this.areadata.length !== 397) {
            for (let j in this.citydata) {
              let curValue = this.citydata[j].value + '';
              if (this.areadata.indexOf(curValue) < 0) {
                this.citydata[j].checkAll = false;
              } else {
                this.citydata[j].checkAll = true;
              }
              let count = 0;
              for (let k in this.citydata[j].children) {
                let curChildValue = this.citydata[j].children[k].value + '';
                if (this.areadata.indexOf(curChildValue) < 0) {
                  this.citydata[j].children[k].isCheck = false;
                } else {
                  this.citydata[j].children[k].isCheck = true;
                  ++count;
                }
              }
              this.citydata[j].isCheckChildrenCount = count;
            }
          } else {
            for (let j in this.citydata) {
              this.citydata[j].checkAll = false;
              for (let k in this.citydata[j].children) {
                this.citydata[j].children[k].isCheck = false;
              }
              this.citydata[j].isCheckChildrenCount = 0;
            }
          }
      },
    handleCheckProvince(param) {
      if (!param.checkAll) {
        for (let i in param.children) {
          param.children[i].isCheck = true;
        }
        param.isCheckChildrenCount = param.childrenCount;
      } else {
        for (let i in param.children) {
          param.children[i].isCheck = false;
        }
        param.isCheckChildrenCount = 0;
      }
      param.checkAll = !param.checkAll;
      this.emitData();
    },
    handleCheckCity(parent, param) {
      param.isCheck = !param.isCheck;
      let childrenLen = parent.children.length;
      let count = 0;
      for (let i in parent.children) {
        if (parent.children[i].isCheck === true&&parent.children[i].value!=null) {
          ++count;
        }
      }
      if (count === childrenLen) {
        parent.checkAll = true;
      } else {
        parent.checkAll = false;
      }
      parent.isCheckChildrenCount = count;
      this.emitData();
    },
    emitData() {
      let vm = this;
      let rstareadata = {
        index: [],
        name: []
      };
      for (let j in vm.citydata) {
        if (vm.citydata[j].checkAll === true&&vm.citydata[j].value!=null) {
          rstareadata.index.push(vm.citydata[j].value);
          rstareadata.name.push(vm.citydata[j].label);
        }
        for (let k in vm.citydata[j].children) {
          if (vm.citydata[j].children[k].isCheck === true&&vm.citydata[j].children[k].value!=null) {
            rstareadata.index.push(vm.citydata[j].children[k].value);
            rstareadata.name.push(vm.citydata[j].children[k].label);
          }
        }
      }
      this.$emit('showareadata', rstareadata);
    }
  }
}
</script>
<style>
.clearfix {
  zoom: 1;
}
.clearfix:after {
  content: ' ';
  display: block;
  height: 0;
  clear: both;
  overflow: hidden;
}
.region-wrapper {
  float: left;
  margin-bottom: 15px;
  width: 25%;
}
.region-wrapper .city-wrapper {
  width: 200px;
  overflow: hidden;
}
.region-wrapper .city-wrapper .city-item {
  float: left;
  margin: 5px 10px 5px 0px;
  min-width: 80px;
}
.region-wrapper .select-count {
  color: #2d8cf0;
}
</style>