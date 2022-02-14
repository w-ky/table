<template>
  <div class="table-container">
    <el-table
      :data="data"
      style="width: 100%"
      :height="setting.height"
      v-loading="setting.isLoading"
      :border="setting.isBorder"
      :stripe="setting.isStripe"
      @selection-change="(e) => handleClick('select', e)"
      @sort-change="(e) => handleClick('sort',e)"
      @row-click ="(e,column) => setting.isRowClick ? handleClick('row',e,column) : () => {}"
    >
      <el-table-column
        v-if="setting.isSelection"
        width="55"
        type="selection"
        fixed
        align="center"
      ></el-table-column>
      <el-table-column
        v-if="setting.isIndex"
        :index="1"
        type="index"
        fixed
        align="center"
      ></el-table-column>
      <template v-for="item in header">
        <el-table-column
          v-if="item.prop !== 'action'"
          :key="item.prop + item.type"
          :type="item.type"
          :prop="item.prop"
          :label="item.label"
          :width="item.width"
          :fixed="item.fixed"
          :sortable="item.sortable"
          :show-overflow-tooltip="item.isTooltip"
          :align="item.align || 'center'"
        >
          <template slot-scope="{row}">
            <slot :name="item.slotType" :row="row" />
            <span v-if="!item.slotType">{{row[item.prop] === null ? '--' : row[item.prop]}}</span>
          </template>
        </el-table-column>
        <el-table-column
          v-else
          :key="item.prop + item.label"
          :label="item.label"
          :width="item.width"
          :fixed="item.fixed"
          align="center"
        >
          <template slot-scope="{row}">
            <template>
              <el-button size="mini" v-for="(btnItem,index) in item.arr" type="text" :key="index" 
                v-allow="btnItem.permission" 
                @click.stop="handleClick(btnItem.type, row)" >
                {{ btnItem.name }}
              </el-button>

              <slot :name="item.slotType" :row="row" />

            </template>
          </template>
        </el-table-column>
      </template> 
    </el-table>
    <div class="pagination-wrap">
      <el-pagination
        v-if="setting.isPagination"
        style="text-align: right; padding: 6px 0"
        @size-change="(e) => handleClick('pageSize', e)"
        @current-change="(e) => handleClick('currentPage', e)"
        :current-page="setting.currentPage"
        :page-sizes="[20, 50, 100, 200]"
        :page-size="setting.pageSize"
        layout="total,prev, pager, next,sizes,jumper"
        :total="setting.total"
      >
      </el-pagination>
    </div>
  </div>
</template>

<script>
import { mapGetters } from 'vuex';
export default {
  name: "Table",
  props: {
    data: {
      type: Array,
      default: () => [],
    },
    header: {
      type: Array,
      require: true,
    },
    setting: {
      type: Object,
      default: () => {
        return {
          height: null,
          isBorder: false,
          isStripe:true,
          isLoading: false,
          isIndex: false,
          isSelection: false,
          isTooltip:false,
          isPagination: false,
          isRowClick:false,
          currentPage: 1,
          pageSize:20,
          total: 20,
        };
      },
    },
  },
  computed: {
    ...mapGetters(["userInfo"]),
    currentPage() {
      return this.setting.currentPage;
    },
  },
  watch: {
    setting: {
      handler(e) {
        if (typeof e.isStripe === "undefined") e.isStripe = true;
        if (typeof e.total === "undefined") e.total = 20;
      },
      immediate: true,
    },
  },
  data() {
    return {};
  },
  mounted() {
    
  },
  methods: {
    handleClick(type, e,column) {
      this.$emit("select", type, e,column);
    },
  },
};
</script>

<!-- Add "scoped" attribute to limit CSS to this component only -->
<style lang='scss' scoped>
.pagination-wrap {
  margin:16px 16px 0 0;
  // position: fixed;
  // bottom: 22px;
  // right: 20px;
}

</style>
