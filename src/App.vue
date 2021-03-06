<template>
  <div>
    <div class="container">
      <input class="item" placeholder="APIKey" v-model="key" />
      <button @click="fetch">fetch</button>
    </div>
    <vue-good-table
      :columns="columns"
      :rows="rows"
      :group-options="{
        enabled: true,
      }"
    />
  </div>
</template>

<script>
import "vue-good-table/dist/vue-good-table.css";
import { VueGoodTable } from "vue-good-table";
import { fetchAPI } from "./js/api";

const filterOptions = {
  enabled: true,
  placeholder: "Filter",
  filterValue: "",
  filterDropdownItems: [],
  filterFn: (data, filterString) => {
    return data && String(data).includes(filterString);
  },
  trigger: "", // ""(keyup) | "enter"
};
// const paginationOptions = {
//   enabled: true,
//   mode: "records",
//   perPage: 10,
//   position: "bottom",
//   perPageDropdown: [10, 25],
//   dropdownAllowAll: false,
//   setCurrentPage: 2,
//   nextLabel: "next",
//   prevLabel: "prev",
//   rowsPerPageLabel: "Rows per page",
//   ofLabel: "of",
//   pageLabel: "page", // for 'pages' mode
//   allLabel: "All",
// };
export default {
  name: "App",
  components: {
    VueGoodTable,
  },
  data() {
    return {
      key: "",
      columns: [
        // {
        //   label: "Attacker",
        //   field: "attacker_name",
        //   filterOptions,
        // },
        // {
        //   label: "Defender",
        //   field: "defender_name",
        //   filterOptions,
        // },
        {
          label: "Attacker",
          field: "attacker_id",
          filterOptions,
          headerField: () => "Total",
        },
        {
          label: "Defender",
          field: "defender_id",
          filterOptions,
          headerField: (rowObj) => rowObj.children.length,
        },
        {
          label: "_Result_",
          field: "result",
          filterOptions: {
            enabled: true,
            placeholder: "Filter",
            filterValue: "",
            filterDropdownItems: [
              "Escape",
              "Stalemate",
              "Lost",
              "Mugged",
              "Attacked",
            ],
            trigger: "", // ""(keyup) | "enter"
          },
        },
        {
          label: "Log",
          field: "log",
          html: true,
        },
      ],
      rows: [],
    };
  },
  methods: {
    fetch() {
      let to = new Date().getTime();
      let from = to - 1000 * 60 * 60 * 24 * 7;
      var that = this;
      fetchAPI(this.key, "user", ["attacksfull"], null, from, to).then(
        (data) => {
          let { attacks } = data;
          that.parseData(attacks, true);
        }
      );
    },
    parseData(attacks, enableGroup) {
      let rows = [];
      for (const id in attacks) {
        let attack = attacks[id];
        attack.log = `<a href="https://www.torn.com/loader.php?sid=attackLog&ID=${
          attack.code
        }">${attack.code.substring(0, 6)}...</a>`;
        rows.push(attack);
      }
      console.log(`total records: ${rows.length}`);
      if (enableGroup) {
        this.rows = [
          {
            // mode: "span",
            children: rows,
          },
        ];
      } else {
        this.rows = rows;
      }
    },
  },
  mounted() {},
};
</script>

<style>
.container {
  display: flex;
}
.item {
  flex: 1;
}
</style>
