<template>
    <q-page padding>
        <h5 class="text-center">Fill The Following Details</h5>
        <q-form class="my-form q-ma-md q-pa-lg" @submit.prevent="submitForm">
            <div class="row q-pa-md">
                <div class="col q-mr-md">
                    <q-input
                        outlined
                        clearable
                        v-model="form.fname"
                        label="Enter First Name"
                    />
                </div>
                <div class="col">
                    <q-input
                        outlined
                        clearable
                        v-model="form.lname"
                        label="Enter Last Name"
                    />
                </div>
            </div>
            <div class="row q-pa-md">
                <div class="col q-mr-md">
                    <q-input
                        outlined
                        clearable
                        v-model="form.mobile"
                        label="Enter Phone Number"
                    />
                </div>
                <div class="col">
                    <q-input
                        outlined
                        clearable
                        v-model="form.email"
                        label="Email"
                     />
                </div>
            </div>            
            <div class="row q-pa-md">
                <div class="col">
                    <q-input
                        outlined
                        clearable
                        v-model="form.partyName"
                        label="Enter Party Name"
                    />
                </div>
            </div>

            <div class="row q-pa-md q-mb-xl">
                <div class="col q-mr-md">
                   <q-input
                        label="Duration From"
                        outlined
                        clearable
                        dense
                        v-model="form.fromDate"
                    >
                        <template v-slot:append>
                            <q-icon name="event" class="cursor-pointer">
                                <q-popup-proxy >
                                    <q-date
                                        minimal
                                        mask="DD/MM/YYYY"
                                        v-model="form.fromDate"
                                    />
                                </q-popup-proxy>
                            </q-icon>
                        </template>
                    </q-input>
                </div>
                <div class="col">
                   <q-input
                        label="Duration to"
                        outlined
                        clearable
                        dense
                        v-model="form.toDate"
                    >
                        <template v-slot:append>
                            <q-icon name="event" class="cursor-pointer">
                                <q-popup-proxy >
                                    <q-date
                                        minimal
                                        mask="DD/MM/YYYY"
                                        v-model="form.toDate"
                                    />
                                </q-popup-proxy>
                            </q-icon>
                        </template>
                    </q-input>  
                </div>
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
                    mobile: '',
                    email: '',
                    partyName: '',
                    fromDate: '',
                    toDate: '',
                },
                columns: [
                    {
                        name: 'Date',
                    },
                    {
                        name: 'Txn Type',
                    },
                    {
                        name: 'Invoice/ Bill no',
                    },
                    {
                        name: 'Total Amount',
                    },
                    {
                        name: 'Recieved/ Paid Amount',
                    },
                    {
                        name: 'Txn Balance',
                    },
                    {
                        name: 'Recievable Balance',
                    },
                    {
                        name: 'Payable Balance',
                    },                    
                ],
                data: [
                    {
                        date: '',
                        txnType: "Beginning Balance",
                        invoice: '',
                        totalAmount: '',
                        paidRecieveAmount: '',
                        txnBalance: '',
                        recievableBalance: 88688868754,
                        payableBalance: '',
                    },
                    {
                        date: '29/04/2021',
                        txnType: "Sale",
                        invoice: '2',
                        totalAmount: 888888888,
                        paidRecieveAmount: 888888888,
                        txnBalance: 0,
                        recievableBalance: '',
                        payableBalance: '',
                    },
                ]
            }
        },
        methods: {
            submitForm() {
                const doc = new jsPDF()

                this.data[0].date = this.form.fromDate
                this.data[1].recievableBalance = this.data[0].recievableBalance

                this.convertCurrency(this.data)

                doc.setFont('helvetica', "bold")
                doc.setFontSize(20)
                doc.text(this.form.fname + " " + this.form.lname, 105, 10, "center")

                doc.setFont("times", "normal")
                doc.setFontSize(14)
                doc.text("Phone: " + this.form.mobile + " Email: " + this.form.email, 105, 20,null, null, "center")

                doc.setFont("helvetica", "bold")
                doc.setFontSize(25)
                doc.text("Party Statement", 105, 40, "center")
                doc.line(72, 42, 140, 42)

                doc.setFont('times', 'normal')
                doc.setFontSize(14)
                doc.text('Party Name: ' + this.form.partyName, 10, 50)
                doc.text("Duration: From " + this.form.fromDate + " to " + this.form.toDate, 10, 60)

                doc.autoTable({
                    startY: 70,
                    theme: "grid",
                    head: [this.columns.map((column) => {return column.name})],
                    body: this.data.map((dataItem) => {return Object.values(dataItem)})
                })
                let finalY = doc.lastAutoTable.finalY

                doc.setFont('times', 'bold')
                doc.setFontSize(10)
                doc.text('Total Receivable balance: ' + this.data[1].recievableBalance, 200, finalY + 5, null, null, "right")

                let bottomY = doc.internal.pageSize.height
                
                doc.setFont('times', 'normal')
                doc.setTextColor(0,0,255)
                doc.text('www.vyaparapp.in', 10, bottomY - 10)

                doc.addImage("image.png", 'PNG', 150, bottomY - 50, 200, bottomY)

                window.open(doc.output("bloburl"), "_blank");
                // doc.save("1.pdf")
            },
            formatCurrency(value) {
                if (value == '') value = 0

                let newValue = value.toFixed(2).replace(/(\d)(?=(\d{2})+\d\.)/g, '$1,')
                return "INR " + newValue

            },
            convertCurrency(array) {
                array.forEach(element => {
                    element.totalAmount = this.formatCurrency(element.totalAmount)
                    element.paidRecieveAmount = this.formatCurrency(element.paidRecieveAmount)
                    element.txnBalance = this.formatCurrency(element.txnBalance)
                    element.recievableBalance = this.formatCurrency(element.recievableBalance)
                    element.payableBalance = this.formatCurrency(element.payableBalance)
                });

            },
        }
    }
</script>