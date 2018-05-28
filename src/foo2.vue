<template>
  <div class="wrapper">
    <input ref="input" class="input" type="search" @input="onInput" @return="picker" return-key-type="search" placeholder="御書名或關鍵字" v-model="search" />
    <text @click="scrollerTo" class="button">Go to 20</text>
    <scroller class="scroller">
      <div class="row" v-for="(name, index) in rows" :ref="'item'+index">
        <text class="text content" :ref="'text'+index">{{name}}</text>
      </div>
      <div class="container">
        <text class="content">{{context}}</text>
      </div>
    </scroller>
  </div>
</template>

<script>
const picker = weex.requireModule('picker')
const dom = weex.requireModule('dom')
import articles from './yushu.js'
import _ from 'lodash'

export default {
	data() {
		return {
			search: '',
			context: '',
			articles: articles.RECORDS,
			nameList: ['test', 'test2'],
			rows: []
		}
	},
	methods: {
		picker() {
			var search_word = this.search.replace(/\s+/g, ' ')
			var pattern = /[\u3105-\u3129\u02CA\u02C7\u02CB\u02D9a-z]/i
			var isChinese = !pattern.test(search_word)

			if (isChinese && search_word.length) {
				var result = this.articles.filter(x => {
					var re = new RegExp(search_word.replace(' ', '.*'), 'i')
					return x.name.match(re) || x.context.match(re)
				})

				if (result.length) {
					this.nameList = []
					result.forEach(x => {
						this.nameList.push(x.name)
					})
					// this.result = this.nameList
					picker.pick(
						{
							index: 0,
							items: this.nameList
						},
						event => {
							if (event.result === 'success') {
								var index = event.data
								var id = result[index]._id
								this.context = _.find(this.articles, ['_id', id]).context.replace(/<br>/g, '\n\n')
							}
						}
					)
				} else {
				}
			}
		},
		scrollerTo() {
			const el = this.$refs.item20[0]
			dom.scrollToElement(el, { offset: 0 })
		}
	},
	created() {
		for (let i = 0; i < 30; i++) {
			this.rows.push('row ' + i)
		}
	},
	mounted() {}
}
</script>

<style>
.input {
	font-size: 50px;
	height: 110px;
	padding-top: 5px;
	padding-bottom: 5px;
	padding-left: 20px;
	padding-right: 20px;
	/*width:85%;*/
	color: #666666;
	border-width: 2px;
	border-style: solid;
	border-color: #41b883;
}

.wrapper {
	flex-direction: column;
	justify-content: center;
}

.scroller {
	float: right;
	z-index: 10000;
	background-color: #000;
}

.content {
	font-size: 45;
	color: #efd700;
	flex-direction: row;
}

.container {
	padding-top: 15px;
	padding-bottom: 15px;
	padding-left: 15px;
	padding-right: 15px;
}
</style>