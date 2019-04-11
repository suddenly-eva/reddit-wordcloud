<template>
  <div id="app">
    <vue-word-cloud
      :animation-duration="1200"
      animation-easing="ease-in-out"
      :animation-overlap="1/2"
      :color="color"
      :enter-animation="{opacity: 0}"
      font-family="Nunito, sans-serif"
      :font-size-ratio="1/8"
      :leave-animation="{opacity: 0}"
      :spacing="1/2"
      :words="rendered_words"
    />
  </div>
</template>

<script>
import VueWordCloud from 'vuewordcloud'

const punct = /(?:[()\-&$![\]{}"'‘’,.]+(?:\s|$)|(?:^|\s)[?:()\-&$#![\]{}"'‘’,.]+)/g
const common = 
  {
    'THE' : true,
    'FOR' : true,
    'TO' : true,
    'OF' : true,
    'MY' : true,
    'A' : true,
    'AS' : true,
    'AND' : true,
    'HAS' : true,
    'WITH' : true,
    'ON' : true,
    'WHAT' : true,
    'IN' : true,
    'ARE' : true,
    'YOU' : true,
    'I' : true,
    'AT' : true,
    'IS' : true,
    'THIS' : true,
    'YOUR' : true,
    'FROM' : true,
    'DO' : true,
    'IT' : true,
    'THAT' : true,
    'NOT' : true,
    'HER' : true,
    'HIS' : true,
    '|' : true,
    'SO' : true,
    'DOES' : true,
    'BE' : true,
    'ABOUT' : true,
    'CAN' : true,
    'OR' : true,
    'WHY' : true,
    'YES' : true,
    'AFTER' : true,
    'SOME' : true,
    'WOULD' : true,
    'BUT' : true,
    'ME' : true,
    '@' : true,
    'HE' : true,
    'SHE' : true,
    'HOW' : true,
    'ONE' : true,
    'AN' : true,
    ':' : true,
    'I\'M' : true,
    'WHEN' : true,
    'WAS' : true,
    'HAVE' : true,
    'BEEN' : true,
    'OUT' : true,
    'WERE' : true,
    'MORE' : true,
    'BY' : true,
    'WITHIN' : true,
    'IF' : true,
    'WILL' : true,
    'IT\'S' : true,
    'LIKE' : true,
    'NEW' : true,
    'YET' : true,
    'JUST' : true,
    'BACK' : true,
    '_' : true,
    'TOP' : true,
    'GET' : true,
    'THEY' : true,
    'HAD' : true,
    'ALL' : true,
    'HIM' : true,
    'HERE' : true,
    'OFF' : true,
    'WHO' : true,
    'WHERE' : true,
    'UP' : true,
    'INTO' : true,
    '/' : true,
    'ANY' : true,
    'DON\'T' : true,
    'GO' : true,
    'SHOULD' : true,
    'THAN' : true,
    'AGO' : true,
    'WHAT\'S' : true,
    'THEN' : true,
    'WE' : true,
    '【' : true,
    'ANYONE' : true,
    // 'HELP' : true,
    'THEM' : true,
    'ITS' : true,
    'THERE' : true,
    '||' : true,
    'NOW' : true,
    'THEIR' : true,
    'SOMETHING' : true,
    'SOMEONE' : true,
    'DID' : true,
    'GOT' : true,
    'THING' : true,
    'THESE' : true,
    'YOU\'RE' : true,
    'AM' : true,
    '+' : true,
    '-' : true,
    'H' : true,
    'IT’S' : true,
    'I’M' : true,
    'DON’T' : true,
    'VERY' : true,
    'TOO' : true,
    'WANT' : true,
    '//' : true,
    'WHICH' : true,
    'STILL' : true,
    '–' : true,
    'OUR' : true,
    '?' : true,
    ';' : true,
    '—' : true,
    '::' : true,
    'ELSE' : true,
    'ONLY' : true,
    'COULD' : true,
    'DOESN\'T' : true,
    '...' : true,
    'THOSE' : true,
    'WENT' : true,
    'NO' : true,
    'BEING' : true,
    'LOOKING' : true,
    'BECAUSE' : true,
    'GOING' : true,
    '.' : true,
    'I’VE' : true,
    'EVER' : true,
    'W' : true,
    'REALLY' : true,
    'WHAT’S' : true,
    'YOU’VE' : true,
    'CAN\'T' : true,
    'ALSO' : true,
    'THAT\'S' : true,
    'DOESN’T' : true,
    'THERE\'S' : true,
    'BEFORE' : true,
    'ABLE' : true,
    'CAN’T' : true,
    'ISN’T' : true,
    'ISN\'T' : true,
    'I\'LL' : true,
    'I’LL' : true,
    'HERE\'S' : true,
    'HERE’S' : true,
  }

let wordmap = {}
let words = []
let socket = new WebSocket('ws://reddit-wordcloud.url.chat')

socket.onmessage = (event) => {

  const new_words = 
    event.data
    .toUpperCase()
    .split(' ')
    .map(s => s.replace(punct, ' ').trim())

  for (let word of new_words) {
    if (word && !common[word] && wordmap[word] == undefined) {
      const new_word = { text: word, weight: 1 }
      words.push(new_word)
      wordmap[word] = new_word
    }
    else if (word && !common[word] ) {
      ++wordmap[word].weight
    }
  }

}

const colors = ['#8cc679', '#c27af7', '#4ea3e4', '#dd9666', '#e8587c']

export default {
  name: 'app',
  data() {
    return {
      rendered_words : []
    }
  },
  mounted() {
    setInterval(this.refresh, 10000)
  },
  computed: {
    color() {
      return () => {
        let index = Math.floor(Math.random() * Math.floor(5))
        return colors[index]
      }
    }
  },
  methods: {
    refresh() {
      for (let w of words) {
        w.weight /= 2
      }
      this.rendered_words = JSON.parse(JSON.stringify(words.filter(w => w.weight > 0.5)))
    }
  },
  components: {
    'vue-word-cloud' : VueWordCloud,
  },
}
</script>

<style>
@import url('https://fonts.googleapis.com/css?family=Nunito');

html, body, #app {
  width: 100%;
  height: 100%;
  margin: 0;
  padding: 0;
  background-color: #222;
}

#app {
  overflow: hidden;
}

</style>
