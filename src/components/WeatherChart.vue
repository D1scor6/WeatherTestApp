

<script>
import {Line} from 'vue-chartjs'

export default {
  name: "WeatherChart",
  extends: Line,
  props: ['chardata', 'options'],
  mounted() {
    this.renderChart(this.data, this.options)
  }
}

</script>