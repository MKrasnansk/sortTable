<template>
    <!-- <h1>{{ salaryAfterTax }}</h1> -->
    <!-- navigacia horna success lista -->
    <nav class="navbar navbar-dark bg-success">
        <span class="navbar navbar-brand mb-0 h1 ">Inventory data</span>
    </nav>
    <div class="container">
        <br />
        <div class="row">
            <div class="col-md-12">
                <!-- select vmodel nadstavuje filterValue na search alebo available pomocou metod searchSelected() availableSelected()   -->
                <label>Sort by: </label>
                <select v-model="filterValue">
                    <option value="">Show all</option>
                    <option value="available">Available</option>
                    <option value="search">Search</option>
                </select>
            </div>
        </div>
        <br />
        <div class="row">
            <div class="col-md-12">
                <!-- tuto sekciu zobrazi vshow ak availableSelected je true  -->
                <span v-show="availableSelected()">
                    <label>Available:</label>
                    <!-- rovnaky name pre oznacenie vzdy len jedneho z nich -->
                    <!-- vbind jeden true druhy false pre nadstavenie s priradenou hodnotou yes alebo no z hodnoty v tabulke -->
                    <input
                        :value="true"
                        type="radio"
                        name="available"
                        v-model="radioValue"
                    />
                    <label> Not available:</label>
                    <input
                        :value="false"
                        type="radio"
                        name="available"
                        v-model="radioValue"
                    />
                </span>
            </div>
        </div>
        <!-- tuto sekciu zobrazi vshow ak searchSelected je false  -->
        <div v-show="!searchSelected()" class="row">
            <div class="col-md-12">
                <table class="table">
                    <thead>
                        <th>Name</th>
                        <th>Price</th>
                        <th>Rating</th>
                        <th>Available</th>
                        <th>Seller</th>
                        <th>Product Id</th>
                        <th>Discount %</th>
                        <th>Discounted price</th>
                    </thead>
                    <tbody>
                        <!-- ak metoda truefilterByAvailable je true zobrazi hodnoty product -->
                        <!-- vbind na class prima metodu highlight ktora taha podla Available hodnoty v tabulke nazov klasy  -->
                        <!-- vfor preiteruje polom products a vitiahne polozky product a priradi kluc(key) product -->
                        <tr
                            v-show="truefilterByAvailable(product)"
                            :class="highlight(product)"
                            v-for="product in products"
                            :key="product"
                        >
                            <td>{{ product.name }}</td>
                            <td>{{ product.price }}</td>
                            <td>{{ product.rating }}</td>
                            <td>{{ product.available }}</td>
                            <td>{{ product.seller }}</td>
                            <td>{{ product.productid }}</td>
                            <td>{{ product.discount }}</td>
                            <td>{{ priceAfterDiscount(product) }}</td>
                        </tr>
                    </tbody>
                </table>
            </div>
        </div>
        <!-- tuto sekciu zobrazi vshow ak searchSelected je true  -->
        <div v-show="searchSelected()" class="row">
            <div class="col-md-12">
                <label>Search Field:</label>
                <select v-model="searchFilterValue">
                    <option value="name">Name</option>
                    <option value="seller">Seller</option>
                </select>
            </div>
        </div>
        <!-- tuto sekciu zobrazi vshow ak searchSelected je true  -->
        <div v-show="searchSelected()" class="row">
            <div class="col-md-12">
                <label>Search:</label>
                <!-- searchTerm vlozi data pomocov vmodel a dalej snimi pracuje -->
                <input type="text" placeholder="Search" v-model="searchTerm" />
            </div>
        </div>
        <br />
        <!-- tuto sekciu zobrazi vshow ak searchSelected je true  -->
        <div v-show="searchSelected()" class="row">
            <div class="col-md-12">
                <h3>Search results:</h3>

                <table class="table">
                    <thead>
                        <th>Name</th>
                        <th>Price</th>
                        <th>Rating</th>
                        <th>Available</th>
                        <th>Seller</th>
                        <th>Product Id</th>
                        <th>Discount %</th>
                        <th>Discounted price</th>
                    </thead>
                    <tbody>
                        <!-- vshow zobrazi preiterovany tabulku ak je nieco napisane vo vyhladavaciom inpute -->
                        <tr
                            v-show="search(product)"
                            :class="highlight(product)"
                            v-for="product in products"
                            :key="product"
                        >
                            <td>{{ product.name }}</td>
                            <td>{{ product.price }}</td>
                            <td>{{ product.rating }}</td>
                            <td>{{ product.available }}</td>
                            <td>{{ product.seller }}</td>
                            <td>{{ product.productid }}</td>
                            <td>{{ product.discount }}</td>
                            <td>{{ priceAfterDiscount(product) }}</td>
                        </tr>
                    </tbody>
                </table>
            </div>
        </div>
    </div>
</template>

<script>
export default {
    methods: {
        //argument product je ziskany z pola products a priamo product a hodnota je ziskana pomocov v-for a key

        //podmienka: ak je filterValue hodnota search vrati true inak false
        searchSelected() {
            if (this.filterValue === "search") {
                return true;
            } else {
                return false;
            }
        },
        //metoda: prima argument product a defaultne nadstavi result na false
        //podmienka: ak searchTerm nieje prazdny string nadstavi hodnotu pisma na male pismena..
        //vytiahne hodnotu vlozenu do inputu sparsuje na string a male pismo
        //do result sa vlozi hodnota searchField s pridanou funkciou (term)
        //nakoniec cela metoda vrati hodnotu result
        search(product) {
            let result = false;
            if (this.searchTerm !== "") {
                let term = this.searchTerm.toLowerCase();
                let searchField = product[this.searchFilterValue]
                    .toString()
                    .toLowerCase();
                result = searchField.includes(term);
            }
            return result;
        },
        //metoda: prima argument product a vrati hodnotu vypocitanu podla prikladu kde pracuje s hodnotami z product
        priceAfterDiscount(product) {
            return product.price - product.price * (product.discount / 100);
        },
        //metoda: prima argument product a podla podmienky vrati nazov class s ktorym sa dalej pracuje
        highlight(product) {
            if (product.available === "Yes") {
                return "table-success";
            } else {
                return "table-danger";
            }
        },
        //metoda: filterValue hodnota sa rovna available
        availableSelected() {
            return this.filterValue === "available";
        },
        //metoda: prima argument product
        //podmienka: ak je hodnota available yes a zaroven hodnota radioValue true
        //vrati hodnotu true inak false
        truefilterByAvailable(product) {
            if (product.available === "Yes" && this.radioValue === true) {
                return true;
            } else if (
                product.available === "No" &&
                this.radioValue === false
            ) {
                return true;
            } else if (this.filterValue === "") {
                return true;
            } else {
                return false;
            }
        },
    },
    data() {
        return {
            // salary data pre metodu priceAfterDiscount()
            salary: 5000,
            //vyhladavaci input vmodel searchTerm a metoda:search
            searchTerm: "",
            //vmodel searchFilterValue pre vyhladavaci select a v podmienke metody search
            searchFilterValue: "",
            //pre input radio vmodel a podmienk v metode truefilterByAvailable
            radioValue: "",
            //pre select
            filterValue: "",
            //pole produktov s ktorych sa cerpaju hodnoty a tabulka
            products: [
                {
                    name: "iPhone",
                    price: 10000,
                    rating: 4.5,
                    available: "Yes",
                    seller: "Apple",
                    productid: 1,
                    discount: 20,
                },
                {
                    name: "iLaptop",
                    price: 2345,
                    rating: 4.8,
                    available: "No",
                    seller: "ABC Corp",
                    productid: 2,
                    discount: 10,
                },
                {
                    name: "Microwave",
                    price: 378,
                    rating: 4.3,
                    available: "Yes",
                    seller: "XYZ inc",
                    productid: 3,
                    discount: 15,
                },
                {
                    name: "Heater",
                    price: 34,
                    rating: 4.7,
                    available: "Yes",
                    seller: "Marine labs",
                    productid: 4,
                    discount: 5,
                },
                {
                    name: "Book",
                    price: 12,
                    rating: 3.4,
                    available: "Yes",
                    seller: "boookish",
                    productid: 5,
                    discount: 5,
                },
                {
                    name: "Telescope",
                    price: 456,
                    rating: 5,
                    available: "Yes",
                    seller: "Space tech",
                    productid: 6,
                    discount: 8,
                },
                {
                    name: "Charger",
                    price: 10,
                    rating: 3.9,
                    available: "No",
                    seller: "XYZ Electronics",
                    productid: 7,
                    discount: 23,
                },
                {
                    name: "Matress",
                    price: 120,
                    rating: 4.1,
                    available: "Yes",
                    seller: "Sleep Inc",
                    productid: 8,
                    discount: 5,
                },
                {
                    name: "Neon bulb",
                    price: 7,
                    rating: 4.3,
                    available: "Yes",
                    seller: "Sleep Inc",
                    productid: 9,
                    discount: 2,
                },
                {
                    name: "Coffee mug",
                    price: 23,
                    rating: 4.3,
                    available: "No",
                    seller: "The Cafe company",
                    productid: 10,
                    discount: 0,
                },
            ],
        };
    },
    computed: {
        salaryAfterTax() {
            return this.salary - this.salary * 0.2;
        },
    },
};
</script>

<style>
#app {
    font-family: Avenir, Helvetica, Arial, sans-serif;
    -webkit-font-smoothing: antialiased;
    -moz-osx-font-smoothing: grayscale;
    color: #154730;
}
</style>
