<script>
import { Bar } from 'vue-chartjs'
import moment from 'moment'

var last12Months = () => {
  var months = [
    'January',
    'February',
    'March',
    'April',
    'May',
    'June',
    'July',
    'August',
    'September',
    'October',
    'November',
    'December'
  ]

  var today = new Date()
  var orderedMonths = []
  var month = today.getMonth() + 1

  for (var i = 0; i < 12; i++) {
    orderedMonths.push(months[month])
    month === 11 ? (month = 0) : month++
  }

  return orderedMonths
}

var processBills = bills => {
  var oneYearAgo = moment().subtract(1, 'years')
  var months = last12Months()
  var monthsWithValues = new Array(12).fill(0)

  for (var month of monthsWithValues) {
    monthsWithValues[month] = 0
  }

  for (var bill of bills) {
    if (moment(bill.date).isSameOrBefore(oneYearAgo)) {
      continue
    }
    var monthName = moment(bill.date).format('MMMM')
    var indexOfMonth = months.indexOf(monthName)
    monthsWithValues[indexOfMonth] += parseInt(bill.amount)
  }

  return monthsWithValues
}

export default {
  extends: Bar,
  props: ['bills'],
  methods: {
    displayChart: function() {
      this.renderChart({
        labels: last12Months(),
        datasets: [
          {
            label: 'Amount',
            backgroundColor: 'lightblue',
            data: processBills(this.bills)
          }
        ]
      })
    }
  },
  mounted: function() {
    this.displayChart()
  },
  watch: {
    bills: function() {
      this.displayChart()
    }
  }
}
</script>