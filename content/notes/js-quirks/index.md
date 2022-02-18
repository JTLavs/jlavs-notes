---
title: Javascript Quirks
date: "2022-02-15"
---
```javascript
[] + [] // equals ''
```
Arrays are converted to strings and then concatenated. As there are no elements in either of these arrays the concatenation is `"" + ""` which is `""`.
<br><br>

```javascript
true + true // equals 2
```
The `+` operator is interpreted and javascript converts the boolean values to numbers. The numeric value of `true` is `1` so therefore it is just a simple `1+1`.
<br><br>

```javascript
false + false // equals 0
```
Similar to above, the numberic value of `false` is `0`.
<br><br>

```javascript
!![] // equals true
```
`!!` in front of a value converts it to its Boolean representation and the Boolean representation of `[]` is `true`.
<br><br>

```javascript
[,,,].length // equals 3
```
`N` number commas within an array creates `N` number of elements within that array.
<br><br>

```javascript
[1,2,3] + [4,5,6] // equals '1,2,34,5,6'
```
Array are converted to strings and then concatenated. Adding in a trailing comma to the first array would also not affect the outcome.
`[1,2,3,]`+ `[4,5,6]` still equals `'1,2,34,5,6'`.
