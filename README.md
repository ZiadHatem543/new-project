<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <title>CRUDS</title>
    <link rel="stylesheet" href="css/style.css">
</head>
<body>

    <div class="crud">
        <div class="head">
            <h2>CRUDS</h2>
            <p>Product Management System</p>
        </div>


        <div class="inputs">
            <input type="text" placeholder="title" id="title">
            <div class="price">
                <input onkeyup="getTotal()" type="number" placeholder="price" id="price">
                <input onkeyup="getTotal()" type="number" placeholder="taxes" id="taxes">
                <input onkeyup="getTotal()" type="number" placeholder="ads" id="ads">
                <input onkeyup="getTotal()" type="number" placeholder="discount" id="discount">
                <small id="total"></small>
            </div>
            <input type="number" placeholder="count" id="count">
            <input on type="text" placeholder="category" id="category">
            <button id="submit">Create</button>
        </div>


        <div class="outputs">
            <div class="searchBlock">
                <input onkeyup="searchData(this.value)" type="text" id="search" placeholder="Search">
                <div class="btnSearch">
                    <button onclick="getSearchMood(this.id)" id="searchTitle">Search By Title</button>
                    <button onclick="getSearchMood(this.id)" id="searchCategory">Search By Category</button>
                </div>
            </div>

            <div id="deleteAll"></div>

            <table>
                <tr>
                    <th>id</th>
                    <th>title</th>
                    <th>price</th>
                    <th>taxes</th>
                    <th>ads</th>
                    <th>discount</th>
                    <th>total</th>
                    <th>category</th>
                    <th>update</th>
                    <th>delete</th>
                </tr>

                <tbody id="tbody">   
                </tbody>

            </table>

        </div>
    </div>
    
    <script src="js/script.js"></script>
</body>
</html>



*{
  margin: 0;
  padding: 0;
}

body{
  font-family: system-ui;
  background-color: #222;
  color: #fff;
}

.crud{
  width: 80%;
  margin: auto;
}

.head{
  text-align: center;
  text-transform: uppercase;
  margin: 10px 0;
}

input{
  width: 100%;
  height: 30px;
  outline: none;
  border: none;
  background-color: #111;
  border-radius: 4px;
  padding: 4px;
  color: white;
  margin: 7px;
}

input:focus{
  background: #000;
  transform: scale(1.1);
}

.price input{
  width: 20%;
}

#total{
  background: #a00d02;
  padding: 5px 2px;
  border-radius: 4px;
}

#total::before{
  content: 'Total: ';
}

button{
  width: 100%;
  height: 30px;
  border: none;
  cursor: pointer;
  background: #470053;
  color: #fff;
  border-radius: 20px;
  transition: 0.5s;
}

button:hover{
  background: #2d0034;
  letter-spacing: 1px;
}

.btnSearch{
  display: flex;
  justify-content: space-between;
}

.btnSearch button{
  width: 45%;
}

#deleteAll{
   margin: 20px 0;
}

table{
  width: 100%;
  text-align: center;
  margin: 10px 0;
}

table th{
  text-transform: capitalize
}

th , td{
  padding: 7px;
}


let title = document.getElementById('title');
let price = document.getElementById('price');
let taxes = document.getElementById('taxes');
let ads = document.getElementById('ads');
let discount = document.getElementById('discount');
let total = document.getElementById('total');
let count = document.getElementById('count');
let category = document.getElementById('category');
let submit = document.getElementById('submit');

let mood = 'create';
let tmp;


function getTotal(){
    if(price.value !=''){
        let result = ( +price.value + +taxes.value + +ads.value) - +discount.value ;
        total.innerHTML = result ;
        total.style.background = '#040';
    }else{
        total.innerHTML = '' ;
        total.style.background = '#a00d02';
    }
}


// Create Products

let dataPro;

if(localStorage.product != null){
    dataPro = JSON.parse(localStorage.product);
}else{
    dataPro = [];
}



submit.onclick = function(){
    let newProduct = {
        title: title.value,
        price: price.value,
        taxes: taxes.value,
        ads: ads.value,
        discount: discount.value,
        total: total.innerHTML,
        category: category.value,
        count: count.value
    }
    if(title.value !='' && price.value !='' && category.value !='' && newProduct.count <= 100){
   
    if(mood == 'create'){
        if(newProduct.count > 1){
            for(let i = 0 ; i < newProduct.count ; i++){
                dataPro.push(newProduct);
            }
        }else{
            dataPro.push(newProduct);
        }
    }else{
        dataPro[tmp] = newProduct;
        mood = 'create';
        submit.innerHTML = 'Create';
        count.style.display = 'block' ;
    }                                                      
   clearInput();
}
    localStorage.setItem('product' ,     JSON.stringify(dataPro)    );
    clearInput();
    showData()
}


// clear input

function clearInput(){
    title.value = '';
    price.value = '';
    taxes.value = '';
    ads.value = '';
    discount.value = '';
    total.innerHTML = '';
    count.value = '';
    category.value = '';
}


// read

function showData(){
    getTotal()
    let table = '';
    for(let i = 0 ; i < dataPro.length ; i++){
        table += `
        <tr>
            <td>${i}</td>
            <td>${dataPro[i].title}</td>
            <td>${dataPro[i].price}</td>
            <td>${dataPro[i].taxes}</td>
            <td>${dataPro[i].ads}</td>
            <td>${dataPro[i].discount}</td>
            <td>${dataPro[i].total}</td>
            <td>${dataPro[i].category}</td>
            <td><button onclick="updateData(${i})" id="update">Update</button></td>
            <td><button onclick="deleteData(${i})" id="delete">Delete</button></td>
        </tr>
        `;
    }
    document.getElementById('tbody').innerHTML = table;

    let btnDelete = document.getElementById('deleteAll');
    if(dataPro.length > 0){
        btnDelete.innerHTML =
        `
        <button onclick="deleteAllData()">Delete All (${dataPro.length})</button>
        `
    }else{
        btnDelete.innerHTML = '' ;
    }
}

showData();

// delete


function deleteData(i){
    dataPro.splice(i, 1);
    localStorage.product = JSON.stringify(dataPro);
    showData();
}



function deleteAllData(){
    localStorage.clear();
    dataPro.splice(0);
    showData();
}



// update

function updateData(i){
    title.value = dataPro[i].title;
    price.value = dataPro[i].price;
    taxes.value = dataPro[i].taxes;
    ads.value = dataPro[i].ads;
    discount.value = dataPro[i].discount;
    getTotal()
    count.style.display = 'none';
    category.value = dataPro[i].category;
    submit.innerHTML = 'Update' ;
    mood = 'update';
    tmp = i;
    scroll({
        top: 0,
        behavior:'smooth',
    })
}

// search

let searchMood = 'title';

function getSearchMood(id){
   
    let search = document.getElementById('search');
    if(id == "searchTitle"){
        searchMood = 'title';
        
    }else{
        searchMood = 'category';
        
    }
    search.placeholder = 'search by ' + searchMood;
    search.focus();
    search.value = '';
    showData();
}

function searchData(value){
   
    let table = '';
    for(let i = 0 ; i < dataPro.length ; i++){
    if(searchMood == 'title'){

        
            if(dataPro[i].title.toLowerCase().includes(value.toLowerCase())){
               
                table += `
                    <tr>
                        <td>${i}</td>
                        <td>${dataPro[i].title}</td>
                        <td>${dataPro[i].price}</td>
                        <td>${dataPro[i].taxes}</td>
                        <td>${dataPro[i].ads}</td>
                        <td>${dataPro[i].discount}</td>
                        <td>${dataPro[i].total}</td>
                        <td>${dataPro[i].category}</td>
                        <td><button onclick="updateData(${i})" id="update">Update</button></td>
                        <td><button onclick="deleteData(${i})" id="delete">Delete</button></td>
                    </tr>
                    `;

            
        }

    }
   
    else{
        
            if(dataPro[i].category.toLowerCase().includes(value.toLowerCase())){
               
                table += `
                    <tr>
                        <td>${i}</td>
                        <td>${dataPro[i].title}</td>
                        <td>${dataPro[i].price}</td>
                        <td>${dataPro[i].taxes}</td>
                        <td>${dataPro[i].ads}</td>
                        <td>${dataPro[i].discount}</td>
                        <td>${dataPro[i].total}</td>
                        <td>${dataPro[i].category}</td>
                        <td><button onclick="updateData(${i})" id="update">Update</button></td>
                        <td><button onclick="deleteData(${i})" id="delete">Delete</button></td>
                    </tr>
                    `;

            }
        }

    document.getElementById('tbody').innerHTML = table;
    }
}

