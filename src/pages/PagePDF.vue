<template>
    <q-page padding>
        <h5 class="text-center">Fill The Following Details</h5>
        <q-form class="my-form q-ma-md q-pa-lg" @submit.prevent="submitForm">
            <div class="row q-pa-md">
                <div class="col q-mr-md">
                    <q-input
                        outlined
                        v-model="form.fname"
                        label="Enter First Name"
                    />
                </div>
                <div class="col">
                    <q-input
                        outlined
                        v-model="form.lname"
                        label="Enter Last Name"
                    />
                </div>
            </div>
            <div class="row q-pa-md">
                <div class="col q-mr-md">
                    <q-input
                        outlined
                        v-model="form.age"
                        label="Enter Age"
                    />
                </div>
                <div class="col">
                    <q-input
                        outlined
                        v-model="form.mobile"
                        label="Enter Phone Number"
                    />
                </div>
            </div>
            <div class="row q-pa-md">
                <div class="col">
                    <q-input
                        outlined
                        v-model="form.address"
                        label="Address"
                     />
                </div>
            </div>

            <div class="q-pa-md q-mb-lg">
                <q-table
                title="Treats"
                :data="data"
                :columns="columns"
                row-key="name"
                />
            </div>
            
            <div class="absolute-bottom text-center q-pa-md ">
                <q-btn
                    dense
                    color="primary"
                    type="submit"
                    label="Submit"
                    class="full-width"
                 />                    
            </div>
        </q-form>

    </q-page>
</template>

<script>
    import { jsPDF } from 'jspdf'
    import 'jspdf-autotable'

    export default {
        data() {
            return {
                form: {
                    fname: '',
                    lname: '',
                    age: '',
                    mobile: '',
                    address: '',
                },
                columns: [
                    {
                    name: 'name',
                    required: true,
                    label: 'Dessert (100g serving)',
                    align: 'left',
                    field: row => row.name,
                    format: val => `${val}`,
                    sortable: true
                    },
                    { name: 'calories', align: 'center', label: 'Calories', field: 'calories', sortable: true },
                    { name: 'fat', label: 'Fat (g)', field: 'fat', sortable: true },
                    { name: 'carbs', label: 'Carbs (g)', field: 'carbs' },
                    { name: 'protein', label: 'Protein (g)', field: 'protein' },
                    { name: 'sodium', label: 'Sodium (mg)', field: 'sodium' },
                    { name: 'calcium', label: 'Calcium (%)', field: 'calcium', sortable: true, sort: (a, b) => parseInt(a, 10) - parseInt(b, 10) },
                    { name: 'iron', label: 'Iron (%)', field: 'iron', sortable: true, sort: (a, b) => parseInt(a, 10) - parseInt(b, 10) }
                ],
                data: [
                    {
                        name: 'Frozen Yogurt',
                        calories: 159,
                        fat: 6.0,
                        carbs: 24,
                        protein: 4.0,
                        sodium: 87,
                        calcium: '14%',
                        iron: '1%'
                    },
                    {
                        name: 'Ice cream sandwich',
                        calories: 237,
                        fat: 9.0,
                        carbs: 37,
                        protein: 4.3,
                        sodium: 129,
                        calcium: '8%',
                        iron: '1%'
                    },
                    {
                        name: 'Eclair',
                        calories: 262,
                        fat: 16.0,
                        carbs: 23,
                        protein: 6.0,
                        sodium: 337,
                        calcium: '6%',
                        iron: '7%'
                    },
                    {
                        name: 'Cupcake',
                        calories: 305,
                        fat: 3.7,
                        carbs: 67,
                        protein: 4.3,
                        sodium: 413,
                        calcium: '3%',
                        iron: '8%'
                    },
                    {
                        name: 'Gingerbread',
                        calories: 356,
                        fat: 16.0,
                        carbs: 49,
                        protein: 3.9,
                        sodium: 327,
                        calcium: '7%',
                        iron: '16%'
                    },
                    {
                        name: 'Jelly bean',
                        calories: 375,
                        fat: 0.0,
                        carbs: 94,
                        protein: 0.0,
                        sodium: 50,
                        calcium: '0%',
                        iron: '0%'
                    },
                    {
                        name: 'Lollipop',
                        calories: 392,
                        fat: 0.2,
                        carbs: 98,
                        protein: 0,
                        sodium: 38,
                        calcium: '0%',
                        iron: '2%'
                    },
                    {
                        name: 'Honeycomb',
                        calories: 408,
                        fat: 3.2,
                        carbs: 87,
                        protein: 6.5,
                        sodium: 562,
                        calcium: '0%',
                        iron: '45%'
                    },
                    {
                        name: 'Donut',
                        calories: 452,
                        fat: 25.0,
                        carbs: 51,
                        protein: 4.9,
                        sodium: 326,
                        calcium: '2%',
                        iron: '22%'
                    },
                    {
                        name: 'KitKat',
                        calories: 518,
                        fat: 26.0,
                        carbs: 65,
                        protein: 7,
                        sodium: 54,
                        calcium: '12%',
                        iron: '6%'
                    }
                ]
            }
        },
        methods: {
            submitForm() {
                const doc = new jsPDF()

                doc.text("Name: " + this.form.fname + " " + this.form.lname, 10, 10)
                doc.text("Age: " +this.form.age, 10, 20)
                doc.text("Mobile: " + this.form.mobile, 10, 30)
                doc.text("Address: " + this.form.address, 10, 40)

                doc.autoTable({
                    startY: 60,
                    theme: "grid",
                    head: [this.columns.map((column) => {return column.label})],
                    body: this.data.map((dataItem) => {return Object.values(dataItem)})
                })
                window.open(doc.output("bloburl"), "_blank");
                // doc.save("1.pdf")
            },
        }
    }
</script>