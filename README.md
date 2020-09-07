# Usage example

### Init and encrypt
```js
const { Encrypt } = require('../dist')

const basePoint = [
  '55066263022277343669578718895168534326250603453777594175500187360389116729240',
  '32670510020758816978083085130507043184471273380659243275938904335757337482424'
]

const hashLength = '256'

const q = '115792089237316195423570985008687907852837564279074904382605163141518161494337'

const mainKey = [
  '57524282568728082791232576457478242943818952557343747344126049585929786788758',
  '38787228570461587633267528340413718686005272951935064150467570181538022969944'
]

const pedersenBase = [
  '31840000124805594708716908823730049919282872088758887139161784794466556143989',
  '34159713578909946807425318728956240239794987299568203108985430402093588737051'
]

// init
const enc = new Encrypt({
  mainKey,
  basePoint,
  hashLength,
  pedersenBase,
  q,
})

// encrypt bulletin
const encrypted = enc.makeEncryptedBulletin([0, 1, 0])
console.log(JSON.stringify(encrypted))
```

### Encrypt whole bulletin
```js
const votes = [
  [1,0,0], // first question
  [0,1,0], // second question
  [1,0,0]  // third question
]
const encrypted = votes.map((vote) => enc.makeEncryptedBulletin(vote))
```
### Calculate mainKey for 3 decrypts
```js
const dkgCommits = [
  {
    'x': '53223510927926924044821712271603075123426104274557218213053365857469041906786',
    'y': '63812211576732480897403235008742810396190456041389943747378921353807308231394'
  },
  {
    'x': '31487217581766849517295031273817794483626722217369663455992733898856595599183',
    'y': '82028928866030698176659688780576011462734544450397693113073209813925075425706'
  },
  {
    'x': '91417142484544224273770657056726197723275714351299031732568048344701157739195',
    'y': '111868356246544901009252079534910516693040811004478292518237552911866514740597'
  }
].map((point) => [point.x, point.y])

const dkgScalars = [
  '60205190671371004052027574671005427581107002065061497157579481218698888136211',
  '59574457923147341647686899237823522695249872693694764462154125327640876089030',
  '62412122924432091887200600446458193045824422914909193997032220320572755918169',
]

const keyPairs = []
for (let i = 0; i < dkgCommits.length; i++) {
  const privateKey = dkgScalars[i]
  const publicKey = dkgCommits[i]
  keyPairs.push({ publicKey, privateKey })
}

const computedMainKey = enc.calculateMainKey(keyPairs)
console.log(computedMainKey, mainKey)
```
