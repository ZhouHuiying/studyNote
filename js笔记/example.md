### 1.数组转对象

let arr = [];
for (let i = 0; i < 50; i++) {
    arr.push({id: `id${i}`, val: (i % 2 ? true : false)})
}
const map = {}
arr.forEach(item => {
    map[item.id] = item.val
})
