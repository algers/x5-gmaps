<script>
export default {
  name: 'gmapsTransit',
  inject: ['getMap', 'handleError'],
  props: {
  },
  data: () => ({ transit: null }),
  computed: {
    // _options() {
    //   const options = { dissipating: this.dissipating }
    //   if (this.colors) options.gradient = ['rgba(0,0,0,0)', ...this.colors]
    //   if (this.maxIntensity) options.maxIntensity = +this.maxIntensity
    //   if (this.opacity) options.opacity = +this.opacity
    //   if (this.radius) options.radius = +this.radius
    //   return options
    // }
  },
  methods: {
    getData(GMaps) {
      if (this.weightProp)
        return this.items.map(e => ({ location: new GMaps.LatLng(e.lat, e.lng), weight: e[this.weightProp] }))
      return this.items.map(e => new GMaps.LatLng(e.lat, e.lng))
    },
    updateData() {
      this.$GMaps()
        .then(GMaps => this.transit.setData(this.getData(GMaps)))
        .catch(e => this.handleError(e))
    }
  },
  mounted() {
    this.$GMaps()
      .then(GMaps => {
        this.transit = new GMaps.transitLayer({
          map: this.getMap(),
          data: this.getData(GMaps),
        //   options: { ...this._options }
        })
      })
      .catch(e => this.handleError(e))
  },
  watch: {
    items() {
      this.updateData()
    },
    weightProp() {
      this.updateData()
    },
    _options(newVal) {
      this.transit.setOptions(newVal)
    }
  },
  beforeDestroy() {
    if (this.transit) this.transit.setMap(null)
  },
  render: () => null
}
</script>
