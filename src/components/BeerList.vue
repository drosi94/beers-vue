<template>
    <div>
        <v-data-table
                :headers="headers"
                :items="beers"
                hide-actions
                :pagination.sync="pagination"
                :rows-per-page-items="pagination.rowsPerPageItems"
                :total-items="pagination.totalItems"
                class="elevation-1"
        >

            <template v-slot:items="props">
                <td>{{ props.item.name }}</td>
                <td class="text-xs-left">{{ props.item.tagline }}</td>
                <td class="text-xs-left">{{ props.item.first_brewed }}</td>
                <td class="text-xs-left">{{ props.item.description}}</td>
            </template>
        </v-data-table>

        <div class="text-xs-center pt-2">
            <v-switch
                    v-model="isStrongBeer"
                    :label="`Strong Beers: ${isStrongBeer? 'yes' : 'no'}`"
                    @change="computeTotalPages"
            ></v-switch>
        </div>

        <div class="text-xs-center pt-2">
            <v-pagination v-model="pagination.page"
                          :length="pages"
                          @input="getData"
            ></v-pagination>
        </div>
    </div>
</template>

<script>
    import axios from 'axios';

    export default {
        data() {
            return {
                pagination: {
                    page: 1,
                    rowsPerPage: 10,
                    rowsPerPageItems: [10, 20, 40],
                    sortBy: 'name',
                    descending: false
                },
                headers: [
                    {
                        text: 'Name',
                        align: 'left',
                        sortable: false,
                        value: 'name'
                    }, {
                        text: 'Tag Line',
                        value: 'tagline'
                    }, {
                        text: 'First Brewed',
                        value: 'first_brewed'
                    }, {
                        text: 'Description',
                        value: 'description'
                    },
                ],
                beers: [],
                isStrongBeer: false,
            }
        },
        methods: {
            async getData() {
                const response = await axios.get(`https://api.punkapi.com/v2/beers?page=${this.pagination.page}&per_page=${this.pagination.rowsPerPage}` +
                    `&malt=Extra_Pale${this.isStrongBeer ? '&abv_gt=18' : ''}`)
                this.beers = response.data;
            },
            async computeTotalPages() {
                // Get the total items (API doesn't provide the total length on pagination)
                this.pagination.totalItems = (await axios.get(`https://api.punkapi.com/v2/beers?malt=Extra_Pale${this.isStrongBeer ? '&abv_gt=18' : ''}`)).data.length
                this.getData();
            }
        },
        async mounted() {
            try {
                this.computeTotalPages();
            } catch (err) {
                throw err;
            }
        },
        computed: {
            pages() {
                if (this.pagination.rowsPerPage == null ||
                    this.pagination.totalItems == null) {
                    return 0;
                }
                return Math.ceil(this.pagination.totalItems / this.pagination.rowsPerPage)
            }
        }
    }
</script>
