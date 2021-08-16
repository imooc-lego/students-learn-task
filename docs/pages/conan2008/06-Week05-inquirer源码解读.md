```javascript
const EventEmitter = require('events');
const MuteStream = require('mute-stream');
const readline = require('readline');
const { fromEvent } = require('rxjs');
const ansiEscapes = require('ansi-escapes'); // 清屏

const options = {
  type: 'list',
  name: 'name',
  message: 'select your name:',
  choices: [{
    name: 'yaoyao', value: 1
  }, {
    name: 'hengheng', value: 2
  }, {
    name: 'qianqian', value: 3
  }]
};

function prompt(options) {
  return new Promise((resolve, reject) => {
    try {
      const list = new List(options);
      list.render();
      list.on('result', function(result) {
        resolve(result)
      })
    } catch (error) {
      reject(error)
    }
  })
}

class List extends EventEmitter {
  constructor(options) {
    super();
    this.type = options.type;
    this.name = options.name;
    this.message = options.message;
    this.choices = options.choices;
    this.input = process.stdin;
    const ms = new MuteStream();
    ms.pipe(process.stdout);
    this.output = ms;
    this.rl = readline.createInterface({
      input: this.input,
      output: this.output
    });
    this.selected = 0;
    this.height = this.choices.length + 1;
    this.hasSelected = false;
    this.keypress = fromEvent(this.rl.input, 'keypress').forEach(this.onkeypress);
  }

  onkeypress = (keymap) => {
    const keyName = keymap[1].name

    switch(keyName) {
      case 'up':
        this.selected--;
        if (this.selected < 0) {
          this.selected = this.choices.length - 1;
        };
        this.render();
        break;
      case 'down':
        this.selected++;
        if (this.selected > this.choices.length - 1) {
          this.selected = 0;
        };
        this.render();
        break;
      case 'return':
        this.hasSelected = true;
        this.emit('result', this.choices[this.selected]);
        this.render();
        this.close();
        break;
      default: '';
    }
  }

  render() {
    this.output.unmute();
    this.clean();
    this.output.write(this.getContent());
    this.output.mute();
  }

  getContent = () => {
    let title = '';
    if (!this.hasSelected) {
      title = '\x1B[32m?\x1B[39m \x1B[1m' + this.message + '\x1B[22m\x1B[0m \x1B[0m\x1B[2m(Use arrow keys)\x1B[22m\n';
      this.choices.forEach((choice, index) => {
        if (index === this.selected) {
          title += '\x1B[36m❯ ' + choice.name + '\x1B[39m '
          title += index !== this.choices.length - 1 ? '\n' : ''
        } else {
          title += `  ${choice.name}`
          title += index !== this.choices.length - 1 ? '\n' : ''
        }
      })
    } else {
      const name = this.choices[this.selected].name;
      title = '\x1B[32m?\x1B[39m \x1B[1m' + this.message + '\x1B[22m\x1B[0m \x1B[36m' + name + '\x1B[39m\x1B[0m \n';
    }

    return title;
  }

  clean() {
    const lines = ansiEscapes.eraseLines(this.height);
    this.output.write(lines);
  }

  close() {
    this.output.unmute();
    this.rl.output.end();
    this.rl.pause();
    this.rl.close();
  }
}

prompt(options).then(answers => {
  console.log('answers:', answers)
})
```
