
<template>
  <div v-if="loadind" class="PreloaderHolder">
    <Preloader />
  </div>
  <div v-else class="ItemListHolder">
    <div class="FilterHolder">
      <div class="Select">
        <select id="" v-model="selected" name="">
          <option value="1">
            По умолчанию
          </option>
          <option value="2">
            По цене min
          </option>
          <option value="3">
            По цене max
          </option>
          <option value="4">
            По наименованию
          </option>
        </select>
        <div class="Arrow" />
      </div>
    </div>
    <div class="ItemsGrid">
      <Item
        v-for="(Item, index) in SortedItemsArr"
        :id="Item.ItemId"
        :key="index"
        :name="Item.Name"
        :desc="Item.Desc"
        :url="Item.Url"
        :price="Item.Price"
      />
    </div>
  </div>
</template>

<script>
import Item from './Item.vue'
import Preloader from './Preloader.vue'
import { emitter } from '@/assets/js/event-bus.js'

export default {
  name: 'ItemList',
  components: { Item, Preloader },
  data () {
    return {
      Items: [
        {
          Name: 'test1',
          Desc: 'test decs',
          Url: 'https://i.ibb.co/Pjy2VLJ/Item-Photo.png',
          Price: '10000',
          ItemId: 0
        }, {
          Name: 'test3',
          Desc: '',
          Url: 'https://i.ibb.co/Pjy2VLJ/Item-Photo.png',
          Price: '10000',
          ItemId: 1
        }, {
          Name: 'test2',
          Desc: 'test decs',
          Url: 'https://i.ibb.co/Pjy2VLJ/Item-Photo.png',
          Price: '10000',
          ItemId: 2
        }
      ],
      selected: '1',
      loadind: true
    }
  },
  computed: {
    SortedItemsArr () {
      switch (this.selected) {
        case '1':
          return this.Items
        case '2':
          return this.SortCompaniesByPriceDec(this.Items)
        case '3':
          return this.SortCompaniesByPriceInc(this.Items)
        case '4':
          return this.SortCompaniesByName(this.Items)
        default:
          return undefined
      }
    }
  },
  created () {
    emitter.on('ItemAdded', (mess) => {
      mess.ItemId = this.Items.length
      this.Items.push(mess)
      this.SaveStorage(this.Items)
    })
    emitter.on('DeleteItem', (Index) => {
      this.Items.splice(Index, 1)
      this.SaveStorage(this.Items)
    })
  },
  mounted () {
    if (localStorage.getItem('Items')) {
      try {
        this.Items = this.GetDataFromLocalStore(localStorage)
      } catch (e) {
        localStorage.removeItem('Items')
      }
    }
  },
  methods: {
    GetDataFromLocalStore (localStorage) {
      const res = JSON.parse(localStorage.getItem('Items'))
      this.loadind = false
      return res
    },
    SaveStorage (ItemsArr) {
      const parsed = JSON.stringify(ItemsArr)
      localStorage.setItem('Items', parsed)
    },
    SortCompaniesByName (Items) {
      const NeedToSortArr = [...Items]
      NeedToSortArr.sort(function (a, b) {
        a = a.Name
        b = b.Name
        return ((a === b) ? 0 : ((a > b) ? 1 : -1))
      })
      return NeedToSortArr
    },
    SortCompaniesByPriceInc (Items) {
      const NeedToSortArr = [...Items]
      NeedToSortArr.sort(function (a, b) {
        a = parseInt(a.Price)
        b = parseInt(b.Price)
        return ((a === b ? 0 : ((a > b) ? -1 : 1)))
      })
      return NeedToSortArr
    },
    SortCompaniesByPriceDec (Items) {
      const NeedToSortArr = [...Items]
      NeedToSortArr.sort(function (a, b) {
        a = parseInt(a.Price)
        b = parseInt(b.Price)
        return ((a === b ? 0 : ((a > b) ? 1 : -1)))
      })
      return NeedToSortArr
    }
  }
}
</script>

<style lang="scss" scoped>

.PreloaderHolder{
  width: 100%;
  display: flex;
  align-items: center;
  justify-content: center;
}

.ItemListHolder{
  width: 100%;
  display: grid;
  row-gap: 16px;
}

.FilterHolder{
  display: flex;
  justify-content: end;
}

.Select{
  height: 35px;
  position: relative;
  align-items: center;
  display: flex;
  box-shadow: 0px 2px 5px rgba(0, 0, 0, 0.1);
  font-size: 12px;
  color: $mianDisableColor;
}

.Arrow{
  background: url('@/static/DropdownArrow.svg') no-repeat;
  background-size: cover;
  width: 8px;
  height: 6px;
  position: relative;
  right: 21px;
}

.Select select{
  font-size: inherit;
  width: 100%;
  height: 100%;
  padding: $mainClickablePadding;
  -webkit-appearance: none;
  -moz-appearance: none;
  appearance: none;
  background-color: $mainWhite;
}

.ItemsGrid{
  display: grid;
  grid-template-columns: repeat(3, 1fr);
  column-gap: 16px;
  row-gap: 16px;
}

</style>
