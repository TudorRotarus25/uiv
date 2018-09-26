<template>
  <table role="grid" style="width: 100%" @keyup.prevent="onKeyUp">
    <thead>
    <tr>
      <td>
        <btn block size="sm" style="border: none" :aria-label="t('uiv.datePicker.previousYear')" @click="goPrevYear">
          <i :class="iconControlLeft"></i>
        </btn>
      </td>
      <td colspan="4">
        <btn block size="sm" style="border: none" :aria-label="`${year} - ${t('uiv.datePicker.goToYear')}`" @click="changeView()">
          <b>{{year}}</b>
        </btn>
      </td>
      <td>
        <btn block size="sm" style="border: none" :aria-label="t('uiv.datePicker.nextYear')" @click="goNextYear">
          <i :class="iconControlRight"></i>
        </btn>
      </td>
    </tr>
    </thead>
    <tbody>
    <tr v-for="(row, i) in rows">
      <td colspan="2" v-for="(month, j) in row" width="33.333333%">
        <btn
          block
          size="sm"
          style="border: none"
          :aria-label="`${tCell(month)} ${year}`"
          :aria-current="(getBtnClass(i*3+j) === 'primary')"
          :type="getBtnClass(i*3+j)"
          :ref="`dateItem${i * 10 + j}`"
          @focus="setFocusPosition(j, i)"
          @click="changeView(i*3+j)">
          <span>{{tCell(month)}}</span>
        </btn>
      </td>
    </tr>
    </tbody>
  </table>
</template>

<script>
  import Locale from '../../mixins/localeMixin'
  import Btn from './../button/Btn'
  import {isExist} from '../../utils/objectUtils'

  export default {
    components: {Btn},
    mixins: [Locale],
    props: {
      month: Number,
      year: Number,
      iconControlLeft: String,
      iconControlRight: String
    },
    data () {
      return {
        rows: []
      }
    },
    mounted () {
      for (let i = 0; i < 4; i++) {
        this.rows.push([])
        for (let j = 0; j < 3; j++) {
          this.rows[i].push(i * 3 + j + 1)
        }
      }
    },
    methods: {
      tCell (cell) {
        return this.t(`uiv.datePicker.month${cell}`)
      },
      getBtnClass (month) {
        if (month === this.month) {
          return 'primary'
        } else {
          return 'default'
        }
      },
      goPrevYear () {
        this.$emit('year-change', this.year - 1)
      },
      goNextYear () {
        this.$emit('year-change', this.year + 1)
      },
      changeView (monthIndex) {
        if (isExist(monthIndex)) {
          this.$emit('month-change', monthIndex)
          this.$emit('view-change', 'd')
        } else {
          this.$emit('view-change', 'y')
        }
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
