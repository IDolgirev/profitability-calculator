<i18n>
  en:
    amount: 'Amount'
    period_label: 'Period'
    period_name: 'Month'
    overdue_label: 'Overdued?'
    normal_name: 'Normal'
    overdue_name: 'Payment overdue'
  ru:
    amount: 'Сумма платежа'
    period_label: 'Период'
    period_name: 'Месяц'
    overdue_label: 'Платеж просрочен?'
    normal_name: 'Платеж в срок'
    overdue_name: 'Платеж просрочен'
</i18n>
<template>
  <div class="container">
    <b-table
      ref="table"
      :data="paymentsData"
      :bordered="isBordered"
      :striped="isStriped"
      :narrowed="isNarrowed"
      :checkable="isCheckable"
      :loading="isLoading"
      :mobile-cards="hasMobileCards"
      :paginated="isPaginated"
      :per-page="perPage"
      :pagination-simple="isPaginationSimple"
      default-sort="['payment_period', 'asc']"
      :selected.sync="selected"
      :checked-rows.sync="checkedRows">

      <template scope="props">
        <b-table-column field="id" label="ID" width="40" sortable numeric>
            {{ props.row.id }}
        </b-table-column>

        <b-table-column field="payment_period" :label="$t('period_label')" sortable>
          {{ $t('period_name') }} {{ props.row.payment_period }}
        </b-table-column>

        <b-table-column field="overdue" :label="$t('overdue_label')" sortable numeric>
          <span class="tag"
                :class="{ 'is-success': !props.row.overdue,
                          'is-danger': props.row.overdue }">
            {{ props.row.overdue ? $t('overdue_name') : $t('normal_name') }}
          </span>
        </b-table-column>

        <b-table-column field="amount" :label="$t('amount')" sortable numeric>
            {{ props.row.amount.toLocaleString(locale) }}
        </b-table-column>
      </template>
    </b-table> 
  </div>
</template>

<script>
  export default {
    props: [ 'loanId', 'locale'],
    beforeMount() {
      this.getPaymentsList();
      this.$i18n.locale = this.locale;
    },
    watch: {
      locale (val) {
        this.$i18n.locale = val
      }
    },
    data() {
        return {
            paymentsData: [],
            checkedRows: [],
            selected: {},
            isBordered: false,
            isStriped: true,
            isNarrowed: false,
            isCheckable: false,
            isLoading: false,
            hasMobileCards: true,
            isPaginated: false,
            isPaginationSimple: false,
            perPage: 10
        }
    },
    methods: {
      getPaymentsList() {
        this.$http.get(`/api/v1/loans/${this.loanId}/payments`).then(response => {
          let sorted = response.body.sort((el) => { return el.payment_period });
          this.paymentsData = sorted;
        }, response => {
          console.log(response);
        });
      },
      linkToPaymentDetails(id) {
        return `/loans/${this.loanId}/payments/${id}`;
      },
      clearSelected() {
          this.selected = {}
      },
      clearCheckedRows() {
          this.checkedRows = []
      }
    },
}
</script>
