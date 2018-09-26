<template>
  <table role="grid" style="width: 100%" @keyup.prevent="onKeyUp">
    <thead>
    <tr>
      <td>
        <btn block size="sm" style="border: none" :aria-label="t('uiv.datePicker.previousYearGroup')" @click="goPrevYear">
          <i :class="iconControlLeft"></i>
        </btn>
      </td>
      <td colspan="3">
        <btn block size="sm" style="border: none" :aria-label="yearStrLabel">
          <b>{{yearStr}}</b>
        </btn>
      </td>
      <td>
        <btn block size="sm" style="border: none" :aria-label="t('uiv.datePicker.nextYearGroup')" @click="goNextYear">
          <i :class="iconControlRight"></i>
        </btn>
      </td>
    </tr>
    </thead>
    <tbody>
    <tr v-for="(row, i) in rows">
      <td v-for="(year, j) in row" width="20%">
        <btn
          block
          size="sm"
          style="border: none"
          :aria-current="(getBtnClass(year) === 'primary')"
          :type="getBtnClass(year)"
          :ref="`dateItem${i * 10 + j}`"
          @focus="setFocusPosition(j, i)"
          @click="changeView(year)">
          <span>{{year}}</span>
        </btn>
      </td>
    </tr>
    </tbody>
  </table>
</template>

<script>
  import Locale from '../../mixins/localeMixin'
  import Btn from './../button/Btn'

  export default {
    mixins: [Locale],
    components: {Btn},
    props: {
      year: Number,
      iconControlLeft: String,
      iconControlRight: String
    },
    computed: {
      rows () {
        let rows = []
        let yearGroupStart = this.year - this.year % 20
        for (let i = 0; i < 4; i++) {
          rows.push([])
          for (let j = 0; j < 5; j++) {
            rows[i].push(yearGroupStart + i * 5 + j)
          }
        }
        return rows
      },
      yearStr () {
        let start = this.year - this.year % 20
        return `${start} ~ ${start + 19}`
      },
      yearStrLabel () {
        let start = this.year - this.year % 20
        return `${start} ${this.t('uiv.datePicker.to')} ${start + 19}`
      }
    },
    methods: {
      getBtnClass (year) {
        if (year === this.year) {
          return 'primary'
        } else {
          return 'default'
        }
      },
      goPrevYear () {
        this.$emit('year-change', this.year - 20)
      },
      goNextYear () {
        this.$emit('year-change', this.year + 20)
      },
      changeView (year) {
        this.$emit('year-change', year)
        this.$emit('view-change', 'm')
      },
      onKeyUp (event) {
        const keyCode = event.keyCode || event.which

        let x = this.focusPosition.x
        let y = this.focusPosition.y

        switch (keyCode) {
          // left arrow
          case 37: {
            x--
            break
          }
          // up arrow
          case 38: {
            y--
            break
          }
          // right arrow
          case 39: {
            x++
            break
          }
          // down arrow
          case 40: {
            y++
            break
          }
          default: {
            return
          }
        }

        this.changeFocusPosition(x, y)
      },
      changeFocusPosition (x, y) {
        if (this.$refs[`dateItem${y || ''}${x}`]) {
          this.$refs[`dateItem${y || ''}${x}`][0].focus()
        }
      },
      setFocusPosition (x, y) {
        this.focusPosition = { x, y }
      }
    }
  }
</script>
