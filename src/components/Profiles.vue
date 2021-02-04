<template>
  <div class="main-panel">
            <!-- Navbar -->
            <Navbar />
            <!-- End Navbar -->
            <div class="content">
                <div class="container-fluid">
                    <div class="row">

                    <!-- Alerts (included for better user experience and engagement) -->
                        <!-- if all json data were successfully gotten from the records API-->
                        <div class="col-md-12" v-if="success">
                            <div class="alert alert-success" role="alert">
                                <button class="close" @click="closeAlert1">x</button>
                                Success !!! {{profiles.length}} records were found. Feel free to search or filter through them.
                            </div>
                        </div>
                        <!-- if user entered invalid search text including spaces in between the text -->
                        <div class="col-md-12" v-if="invalidInput">
                            <div class="alert alert-warning" role="alert">
                                <button class="close" @click="closeAlert2">x</button>
                                Caution !!! Please enter either the first name, the last name or both (e.g Declan Rice)
                            </div>
                        </div>
                        <!-- if the search text doesn't match any user's first or last name -->
                        <div class="col-md-12" v-if="recordNotFound">
                            <div class="alert alert-info" role="alert">
                                <button class="close" @click="closeAlert3">x</button>
                                Oops !!! No user with that name was found. Please ensure there are no spaces before or after your input
                            </div>
                        </div>
                        <!-- if the search text matches any user's first or last name  -->
                        <div class="col-md-12" v-if="found">
                            <div class="alert alert-primary" role="alert">
                                <button class="close" @click="closeAlert4">x</button>
                                Search result: {{ foundRecord.length }} record(s) was found
                            </div>
                        </div>
                        <!-- shows the number of records that matched the entered text while filtering -->
                        <div class="col-md-12" v-if="filterFound">
                            <div class="alert alert-dark" role="alert">
                                <button class="close" @click="closeAlert5">x</button>
                                Filter result: {{ countFilter.length }} records were found
                            </div>
                        </div>
                        <!-- If there was a connection error while making axios request to the records API  -->
                        <div class="col-md-8" v-if="errored">
                            <div class="alert alert-danger" role="alert">
                                There seem to be a problem with your network. Please check your connection and try again
                            </div>
                        </div>
                    <!-- End Alerts -->

                        <!-- If the request to the records API was successful, display the data in the tables below  -->
                        <div class="col-md-12" v-else>
                            <div class="card strpied-tabled-with-hover">
                                <div class="card-header"><!-- card header -->
                                    <div class="container-fluid">
                                        <div class="row">
                                            <!-- Card title and Sub-Heading -->
                                            <div class="col-12 mb-2">
                                                <h4 class="card-title">Records UI</h4>
                                                <p class="card-category">See all profile records below</p>
                                            </div>

                                            <!-- Search input field -->
                                            <div class="form-group col-8">
                                                <div class="input-group mb-3">
                                                    <input type="text" class="form-control" 
                                                        placeholder="Search by First Name, Last Name or both"
                                                        v-model="search" @keyup.enter="fetchRecord()" required>
                                                    <!-- Search button -->
                                                    <div class="input-group-append">
                                                        <button class="btn btn-outline-secondary" type="button" @click="fetchRecord()">
                                                            <i class="nc-icon nc-zoom-split"></i>
                                                        </button>
                                                    </div>
                                                    <!-- End Search button -->
                                                </div>
                                            </div>
                                            <!-- End Search input field -->

                                            <!-- Filter input field -->
                                            <div class="form-group col-4">
                                                <input type="email" class="form-control" placeholder="Filter by gender or payment method"
                                                v-model="filter" @keydown="filterAlert()">
                                            </div>
                                            <!-- End Filter input field -->
                                        </div>
                                    </div>
                                </div>
                                
                                <!-- card body to show results from the search input field -->
                                <div class="card-body table-full-width" v-if="found">
                                    <div class="container-fluid">
                                        <!-- Alert while getting response from axios request -->
                                        <div class="alert alert-info" role="alert" v-if="loading">
                                            Loading...
                                        </div>
                                        <!-- End Alert -->

                                        <!-- if the axios request was successfully, display data in table -->
                                        <div class="table-responsive table-striped table-hover" style="height:300px" v-else>
                                            <table class="table"> <!-- table-sm -->
                                                <!-- table header -->
                                                <thead class="">
                                                    <tr>
                                                        <th scope="col" class="">#</th>
                                                        <th scope="col">First Name</th>
                                                        <th scope="col">Last Name</th>
                                                        <th scope="col">Gender</th>
                                                        <th scope="col">Payment Method</th>
                                                        <th scope="col">Credit Card No</th>
                                                        <th scope="col">Credit Card Type</th>
                                                        <th scope="col">Email</th>
                                                        <th scope="col">Username</th>
                                                        <th scope="col">Domain Name</th>
                                                        <th scope="col">Phone No.</th>
                                                        <th scope="col">Mac Address</th>
                                                        <th scope="col">URL</th>
                                                        <th scope="col">Last Login</th>
                                                        <th scope="col">Latitude</th>
                                                        <th scope="col">Longitude</th>
                                                    </tr>
                                                </thead>
                                                <!-- End table header -->

                                                <!-- table body -->
                                                <tbody >
                                                    <!-- show record that match the user first or lastname entered -->
                                                    <tr v-for="profile in foundRecord" v-bind:key="foundRecord.indexOf(profile)">
                                                        <th scope="row" class="">{{ foundRecord.indexOf(profile)+1 }}</th>
                                                        <td v-html="highlightMatches( profile.FirstName )"></td>
                                                        <td v-html="highlightMatches( profile.LastName )"></td>
                                                        <td v-html="highlightMatches( profile.Gender )"></td>
                                                        <td v-html="highlightMatches( profile.PaymentMethod )"></td>
                                                        <td v-html="highlightMatches( profile.CreditCardNumber )"></td>
                                                        <td v-html="highlightMatches( profile.CreditCardType )"></td>
                                                        <td v-html="highlightMatches( profile.Email )"></td>
                                                        <td v-html="highlightMatches( profile.UserName )"></td>
                                                        <td v-html="highlightMatches( profile.DomainName )"></td>
                                                        <td v-html="highlightMatches( profile.PhoneNumber )"></td>
                                                        <td v-html="highlightMatches( profile.MacAddress )"></td>
                                                        <td v-html="highlightMatches( profile.URL )"></td>
                                                        <td v-html="highlightMatches( profile.LastLogin )"></td>
                                                        <td v-html="highlightMatches( profile.Latitude )"></td>
                                                        <td v-html="highlightMatches( profile.Longitude )"></td>
                                                    </tr>
                                                </tbody>
                                                <!-- End table body -->
                                            </table>
                                        </div>
                                    </div>
                                    <!-- table pagination -->
                                    <div class="container mt-3">
                                        <nav aria-label="Page navigation example">
                                            <ul class="pagination justify-content-center pagination-lg">
                                                <!-- pagination control -->
                                                <li class="page-item disabled">
                                                    <a class="page-link" @click="prevPage(); pagination()" tabindex="-1">
                                                        <span aria-hidden="true">&laquo;</span>
                                                        <span class="sr-only">Previous</span>
                                                    </a>
                                                </li>
                                                <li class="page-item disabled">
                                                    <a class="page-link">Previous | Next</a>
                                                </li>
                                                <li class="page-item disabled">
                                                    <a class="page-link" @click="nextPage(); pagination()" tabindex="-1">
                                                        <span aria-hidden="true">&raquo;</span>
                                                        <span class="sr-only">Next</span>
                                                    </a>
                                                </li>
                                                <!-- End pagination control -->
                                            </ul>
                                        </nav>
                                    </div>
                                    <!-- End table pagination -->
                                </div>
                                <!-- End card body to show results from the search input field -->

                                <!-- card body to show results from the filter input field -->
                                <div class="card-body table-full-width" v-else>
                                    <div class="container-fluid">
                                        <!-- Alert while getting response from axios request -->
                                        <div class="alert alert-info" role="alert" v-if="loading">
                                            Loading...
                                        </div>
                                        <!-- End Alert -->

                                        <!-- if the axios request was successfully, display data in table -->
                                        <div class="table-responsive table-striped table-hover" v-else>
                                            <table class="table"> <!-- table-sm -->
                                                <!-- table header -->
                                                <thead class="">
                                                    <tr>
                                                        <th scope="col" class="">#</th>
                                                        <th scope="col">First Name</th>
                                                        <th scope="col">Last Name</th>
                                                        <th scope="col">Gender</th>
                                                        <th scope="col">Payment Method</th>
                                                        <th scope="col">Credit Card No</th>
                                                        <th scope="col">Credit Card Type</th>
                                                        <th scope="col">Email</th>
                                                        <th scope="col">Username</th>
                                                        <th scope="col">Domain Name</th>
                                                        <th scope="col">Phone No.</th>
                                                        <th scope="col">Mac Address</th>
                                                        <th scope="col">URL</th>
                                                        <th scope="col">Last Login</th>
                                                        <th scope="col">Latitude</th>
                                                        <th scope="col">Longitude</th>
                                                    </tr>
                                                </thead>
                                                <!-- table header -->

                                                <!-- table body -->
                                                <tbody >
                                                    <!-- show record that match the filtered text entered -->
                                                    <tr v-for="profile in filtered" v-bind:key="profiles.indexOf(profile)">
                                                        <th scope="row" class="">{{ countFilter.indexOf(profile)+1 }}</th>
                                                        <td v-html="highlightMatches( profile.FirstName )"></td>
                                                        <td v-html="highlightMatches( profile.LastName )"></td>
                                                        <td v-html="highlightMatches( profile.Gender )"></td>
                                                        <td v-html="highlightMatches( profile.PaymentMethod )"></td>
                                                        <td v-html="highlightMatches( profile.CreditCardNumber )"></td>
                                                        <td v-html="highlightMatches( profile.CreditCardType )"></td>
                                                        <td v-html="highlightMatches( profile.Email )"></td>
                                                        <td v-html="highlightMatches( profile.UserName )"></td>
                                                        <td v-html="highlightMatches( profile.DomainName )"></td>
                                                        <td v-html="highlightMatches( profile.PhoneNumber )"></td>
                                                        <td v-html="highlightMatches( profile.MacAddress )"></td>
                                                        <td v-html="highlightMatches( profile.URL )"></td>
                                                        <td v-html="highlightMatches( profile.LastLogin )"></td>
                                                        <td v-html="highlightMatches( profile.Latitude )"></td>
                                                        <td v-html="highlightMatches( profile.Longitude )"></td>
                                                    </tr>
                                                </tbody>
                                                <!-- End table body -->
                                            </table>
                                        </div>
                                    </div>

                                    <!-- table pagination -->
                                    <div class="container mt-3">
                                        <nav aria-label="Page navigation example">
                                            <!-- pagination control -->
                                            <ul class="pagination justify-content-center pagination-lg">
                                                <li class="page-item" v-bind:class="{ disabled: isActiveTrue }">
                                                    <a class="page-link" @click="prevPage(); pagination()" tabindex="-1">
                                                        <span aria-hidden="true">&laquo;</span>
                                                        <span class="sr-only">Previous</span>
                                                    </a>
                                                </li>
                                                <li class="page-item disabled">
                                                    <a class="page-link">Previous | Next</a>
                                                </li>
                                                <li class="page-item" v-bind:class="{ disabled: isActiveFalse }">
                                                    <a class="page-link" @click="nextPage(); pagination()" tabindex="-1">
                                                        <span aria-hidden="true">&raquo;</span>
                                                        <span class="sr-only">Next</span>
                                                    </a>
                                                </li>
                                            </ul>
                                            <!-- End pagination control -->
                                        </nav>
                                    </div>
                                    <!-- End table pagination -->
                                </div>
                                <!-- End card body to show results from the filter input field -->
                            </div>
                        </div>
                    </div>
                </div>
            </div>
            <!-- footer -->
            <Footer />
            <!-- End footer -->
  </div>
</template>
<script>
import axios from 'axios';

import Navbar from './Navbar.vue';
import Footer from './Footer.vue';

  export default {
    name: 'Profiles',
    components: {
        Navbar,
        Footer
    },
    data () {
        return {
            currentPage: 1, // works with page size variable to enable pagination
            pageSize: 20, // ensure that only 20 records are shown per page
            profiles: null, // will hold the data from the axios request
            isActiveTrue: false, // for pagination
            isActiveFalse: false, // for pagination
            filter: '', // holds the text input from the filter input field
            filterFound: false,
            search: '', // holds the text input from the search input field
            foundRecord: [], // will hold a record(s) if search input matched
            found: false, //alert
            invalidInput: false, //alert
            recordNotFound: false, //alert
            loading: true, //alert
            success: false, //alert
            errored: false //alert
        }
    },
    mounted () {
        // get json data on page load
        axios.get('https://api.enye.tech/v1/challenge/records')
            .then((response) => {
                // assign json data to profiles variable
                this.profiles = response.data.records.profiles;
                
                // ensures the pagination works by calling the startPagination method every second 
                window.setInterval(() => {
                    this.startPagination();
                },1000);
                
            })
            .catch(error => {
                // check for error and display the error
                console.log(error)
                this.errored = true
            })
            .finally(() => {
                // display success alert if request was successful
                this.loading = false;
                this.success = true;
            })

        
    },
    computed:{
        // get filtered data from profiles based on user input
        filtered : function() {
            // to ensure only 20 records are displayed at a time
            let start = (this.currentPage-1)*this.pageSize;
            let end = this.currentPage*this.pageSize;

            // get text from filter input field
            const filteredTerm = this.filter.toLowerCase();

            // rows represent individual entities in the profile array
            const rows =  this.profiles.filter((row) => {
                const gender = row.Gender.toString().toLowerCase(); // get an array of gender records
                const paymentMethod = row.PaymentMethod.toString().toLowerCase(); // get an array of payment method records

                // ensures female records are not returned together with male records
                if (filteredTerm == "male") { // if the entered text in filter field was 'male'
                    return gender == "male"; // return only records where the gender is male
                }
                else { // else return records where the entered text in filter field matches the gender or payment records 
                    return gender.includes(filteredTerm) || paymentMethod.includes(filteredTerm);
                }
            });
            // only show 20 records at a time
            return rows.slice(start, end);

        },
        // count the number of filtered records
        countFilter : function () {

            return this.profiles.filter((row) => {
                const gender = row.Gender.toString().toLowerCase();
                const paymentMethod = row.PaymentMethod.toString().toLowerCase();
                const filteredTerm = this.filter.toLowerCase();

                // ensures female records are not returned together with male records
                if (filteredTerm == "male") { // if filtered text entered was male
                    return gender == "male";
                }
                else {
                    return gender.match(filteredTerm) || paymentMethod.match(filteredTerm);
                }
            });
        },
        // count the number of pages records will be displayed in
        pageCount: function () {
            return Math.ceil(this.countFilter.length/this.pageSize);  
        }
    },
    watch : {
        // turn off alerts from search input field whenever input is empty
        search : function(val) {
            this.search = val;
            if (this.search == '') {
                this.found = false;
                this.invalidInput = false;
                this.recordNotFound = false;
                this.foundRecord = [];
            }
        }
    },
    methods: {
        // returns records from search input field
        fetchRecord: function() {
            // if search input field is not empty
            if (this.search != '') {
                const searchTerm = this.search.toLowerCase().split(" ");
                // console.log(searchTerm[0]);
                // console.log(searchTerm[1]);

                const searchTermLength = searchTerm.length;// get text from search field

                // ensures not more than two strings are inputted e.g Declan Rice
                if (searchTermLength > 2) {
                    this.invalidInput = true;
                    this.foundRecord = [];
                    console.log("invalid input");
                }
                // if only one string was inputted
                else if (searchTermLength == 1) {
                    this.foundRecord = this.profiles.filter((row) => {
                        const firstName = row.FirstName.toString().toLowerCase();// get all first names from profile record
                        const lastName = row.LastName.toString().toLowerCase();// get all last names from profile record

                        // check if the string matched any first name in record
                        if (firstName.match(searchTerm[0]) != null && lastName.match(searchTerm[0]) == null) {
                            return firstName.match(searchTerm[0]);
                        }
                        // else check if the string match any last name in record
                        else if (firstName.match(searchTerm[0]) == null && lastName.match(searchTerm[0]) != null) {
                            return lastName.match(searchTerm[0]);
                        } 
                        // if the string doesn't match, return false
                        else {
                            return false;
                        }
                    });
                }
                // if two strings were inputted 
                else {

                    this.foundRecord = this.profiles.filter((row) => {
                        const firstName = row.FirstName.toString().toLowerCase();// get all first name from record
                        const lastName = row.LastName.toString().toLowerCase(); // get all last name from record

                        // check if both string matched any record
                        if (firstName.match(searchTerm[0]) != null && lastName.match(searchTerm[1]) != null) {
                            return firstName.match(searchTerm[0]) && lastName.match(searchTerm[1]);
                        } 
                        // else return false
                        else {
                            return false;
                        }   
                    });
                        
                }

                
            }
            // if search input field is empty, turn off alerts
            else if (this.search == '') {
                return this.found = false;
            }

            // show alerts if a record was found
            if (this.foundRecord.length == 1) {
                this.found = true;
                this.recordNotFound = false;
                // alert("found");
            }
            else {
                this.found = false;
                this.recordNotFound = true;
                // alert("Not found");
            }

        },
        // pagination control
        nextPage: function() {
            // move to next page if the current page * page size (1*20) is less than the number of records in profiles
            if((this.currentPage*this.pageSize) < this.profiles.length) this.currentPage++;
        },
        prevPage: function() {
            // move to previous page if the current page * page size (1*20) is more than the number of records in profiles
            if(this.currentPage > 1) this.currentPage--;
        },
        startPagination: function () {
            // ensure pagination is enabled or disabled based on the number of records
            if (this.currentPage == 1 && this.countFilter.length >= this.pageSize){
                this.isActiveTrue = true;
            } else {
                this.isActiveTrue = false;
            }
        },
        // main pagination control
        pagination: function() {
            let pageCount = Math.ceil(this.countFilter.length/this.pageSize);//get the number of pages based on page size(20)

            if(this.currentPage == 1){
                // if page count is 1 disable pagination
                this.isActiveFalse = false;
                this.isActiveTrue = false;
            }

            else if (this.currentPage > 1 && this.currentPage < pageCount) {
                this.isActiveFalse = false;
                this.isActiveTrue = false;
            }
            else {
                this.isActiveFalse = true;
                this.isActiveTrue = false;
            }
        },
        highlightMatches: function(text) {
            // highlight the record that matches user input

            const matchExists = text.toString().toLowerCase().includes(this.filter.toLowerCase());
            if (!matchExists) return text;

            const re = new RegExp(this.filter || this.search, 'ig');
            return text.toString().replace(re, matchedText => `<strong>${matchedText}</strong>`);
        },

        // Alert methods for different use cases
        filterAlert: function() {
            this.filterFound = true;
        },
        // for alert close button
        closeAlert1: function () {
            this.success = false;
        },
        closeAlert2: function () {
            this.invalidInput = false;
        },
        closeAlert3: function () {
            this.recordNotFound = false;
        },
        closeAlert4: function () {
            this.found = false;
        },
        closeAlert5: function () {
            this.filterFound = false;
        }
    }
  }
</script>
<style>

/* make table scrollable */
.table-responsive{height:600px;overflow:scroll;}

/* make table head stay at the top while scrolling down */
thead tr th{
    background: white; 
    position: sticky;
    top: 0;
    z-index: 10;
    box-shadow: 0 4px 0px -1px rgba(0, 0, 0, 0.2);
}

</style>