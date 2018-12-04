<template>
  <div class="container">
    <div class="header">
      Solidity parser
    </div>

    <div class="wrap">
      <div class="source">
        Solidity source code
        <textarea v-model="source"></textarea>
        <button @click="parse">Parse to AST</button>
      </div>
      <div class="ast">
        Parsed AST:
        <hr/>
        <pre><code>{{ast}}</code></pre>
      </div>
      <div class="traverse">
        Traverse tree:
        <hr/>
        <pre><code>{{traverseLog}}</code></pre>
      </div>
    </div>
  </div>
</template>

<script>
/* eslint-disable no-console */

/**
 * @see https://github.com/duaraghav8/sol-explore
 * @see https://github.com/federicobond/solidity-parser-antlr
 */
import parser from 'solidity-parser-antlr'
import explore from 'sol-explore'

const source = `contract MyAnimal {
	event Meow(string message);
	enum Activities {Shushing, Meowing}
	struct Body {
		string name;
		uint dna;
		uint weight;
		Activities activity;
	}
	Body body;
	constructor(string _name, uint _dna, uint _weight) {
		body = Body(_name, _dna, _weight, Activities.Shushing);
	}
	modifier myModifier() {
		_;
	}
	function meow () myModifier {
		emit Meow('meow!');
		body.activity=Activities.Meowing
	}
}`

export default {
  name: 'AstTest',
  data () {
    return {
      source,
      ast: '',
      traverseLog: ''
    }
  },
  methods: {
    parse () {
      this.traverseLog = ''
      try {
        this.ast = parser.parse(this.source)
        this.traverse()
      } catch (e) {
        if (e instanceof parser.ParserError) {
          this.ast = e.errors
        }
      }
    },
    traverse () {
      let traverseLog = ''
      explore.traverse(this.ast, {
        enter(node) {
          console.log(node)
          traverseLog += `Entering ${node.type}\n`
          if (node.type === 'StructDeclaration') {
            this.stopTraversal()
          }
        },
        leave(node) {
          traverseLog += `Leaving ${node.type}\n`
        }
      })
      this.traverseLog = traverseLog
    }
  }
}
</script>

<style scoped>
.header {
  height: 30px;
  text-align: center;
}

.wrap {
  display: table;
  height: 100%;
  width: 100%;
}

.wrap > div {
  display: table-cell;
  height: 100%;

}

.source {
  min-width: 300px;
  width: 30%;
}

.ast {
  padding-left: 50px;
  width: 30%;
}

textarea {
  display: block;
  width: 100%;
  min-height: 500px;
  background: url(http://i.imgur.com/2cOaJ.png);
  background-attachment: local;
  background-repeat: no-repeat;
  padding-left: 35px;
  padding-top: 10px;
  border-color: #ccc;
}

</style>
