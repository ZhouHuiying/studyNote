### 1.数组转对象

let arr = [];
for (let i = 0; i < 50; i++) {
    arr.push({id: `id${i}`, val: (i % 2 ? true : false)})
}
const map = {}
arr.forEach(item => {
    map[item.id] = item.val
})

遍历对象：
obj = {};
for(let key  in obj){
    result.push(obj[key])
}
//根据value 找到对应的key
function findKey (value, compare = (a, b) => a === b) {
    return Object.keys(obj).find(k => compare(obj[k], value))
}