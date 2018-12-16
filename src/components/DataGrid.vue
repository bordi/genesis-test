<!-- @TODO: split to multiple files. -->

<template>
  <div class="grid-table">
    <!-- @TODO: form: move to separate component -->
    <form id="filter">
      Filter: <input name="query" v-model="filterQuery" />
    </form>
    <!-- /form-->

    <table class="table">
      <thead>
      <tr>
        <th v-for="(value, key) in columns"
            :key="key"
            class="table__th"
            @click="sortBy(key)"
            :class="{ 'table__th--active': sortKey === key }">
          {{ value }}
          <span class="arrow" :class="sortOrders[key] > 0 ? 'arrow--asc' : 'arrow--dsc'">
          </span>
        </th>
      </tr>
      </thead>
      <tbody>
      <tr v-for="(item, itemValue) in sortedFilteredData" :key="itemValue">
        <td v-for="(value, key) in columns"
            :key="value"
            class="table__cell">
          {{ item[key] }}
        </td>
      </tr>
      </tbody>
    </table>

    <!-- @TODO: pagination: move to separate component -->
    <div id="pagination">
      <button @click=movePages(-1)>Back</button>
      <p>{{initialRow / perPage + 1}} / {{data.length / perPage}}</p>
      <button @click=movePages(1)>Next</button>
    </div>
    <!-- /pagination-->
  </div>
</template>

<script>
  export default {
    name: 'DataGrid',
    props: {
      columns: Object,
      data: Array,
      filterQuery: String,
      rowsPerPage: Number,
    },
    data: function () {
      const sortOrders = {};

      Object.keys(this.columns).forEach(function (key) {
          sortOrders[key] = 1;
        });

      return {
        sortKey: '',
        sortOrders: sortOrders,
        initialRow: 0,
        perPage: this.rowsPerPage || 10,
      };
    },
    computed: {
      sortedFilteredData () {
        const sortKey = this.sortKey;
        const order = this.sortOrders[sortKey] || 1;
        const filterQuery = this.filterQuery && this.filterQuery.toLowerCase();

        let data = this.data.slice(this.initialRow, this.perPage + this.initialRow);

        if (filterQuery) {
          data = data.filter(function (row) {
            return Object.keys(row).some(function (key) {
              return String(row[key]).toLowerCase().indexOf(filterQuery) > -1
            })
          })
        }

        if (sortKey) {
          data = data.slice().sort((a, b) => {
            let computedA;
            let computedB;

            if (sortKey === 'height' || sortKey === 'mass') {
              computedA = a[sortKey].replace(',', '');
              computedB = b[sortKey].replace(',', '');
            }

            if (sortKey === 'birth_year') {
              computedA = parseFloat(a[sortKey]);
              computedB = parseFloat(b[sortKey]);
            }

            if(sortKey === 'height' || sortKey === 'mass' || sortKey === 'birth_year') {
              a = isNaN(computedA) ? -1 : computedA;
              b = isNaN(computedB) ? -2 : computedB;

              return (a - b) * order;
            }

            a = a[sortKey];
            b = b[sortKey];

            return (a === b ? 0 : a > b ? 1 : -1) * order;
          })
        }

        return data;
      },
    },
    methods: {
      sortBy: function(key) {
        this.sortKey = key;
        this.sortOrders[key] = this.sortOrders[key] * -1;
      },
      movePages: function(amount) {
        const computedInitialRow = amount * this.perPage + this.initialRow;

        if (computedInitialRow >= 0 && computedInitialRow < this.data.length) {
          this.initialRow = computedInitialRow;
        }
      },
    },
  };
</script>

<!-- Add "scoped" attribute to limit CSS to this component only -->
<style>
  :root {
    --bg-color: #fff;
    --border-color: #b96f70;

    --title-bg: #b96f70;
    --title-color: rgba(255, 255, 255, 0.66);
    --title-color--active: #fff;

    --cell-bg: #eef9f6;
  }

  #filter {
    margin-bottom: 10px;
  }

  .table {
    margin: 0 auto;

    border: 2px solid var(--border-color);
    background-color: var(--bg-color);
  }

  .table__th {
    background-color: var(--title-bg);
    color: var(--title-color);
    cursor: pointer;
  }

  .table__th--active {
    color: var(--title-color--active);
  }

  .table__th--active .arrow {
    opacity: 1;
  }

  .table__cell {
    background-color: var(--cell-bg);
  }

  .table__th, .table__cell {
    min-width: 120px;
    padding: 10px 20px;
  }

  .arrow {
    display: inline-block;
    vertical-align: middle;
    width: 0;
    height: 0;
    margin-left: 5px;
    opacity: 0.66;
  }

  .arrow--asc {
    border-left: 4px solid transparent;
    border-right: 4px solid transparent;
    border-bottom: 4px solid #fff;
  }

  .arrow--dsc {
    border-left: 4px solid transparent;
    border-right: 4px solid transparent;
    border-top: 4px solid #fff;
  }

  #pagination {
    display: flex;
    justify-content: center;

    margin-top: 5px;
  }

  #pagination p {
    margin-left: 5px;
    margin-right: 5px;
  }

  #pagination button {
    background-color: var(--title-bg);
    border-color: var(--border-color);
    color: #fff;
  }

</style>
