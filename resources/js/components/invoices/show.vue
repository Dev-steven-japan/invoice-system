<script setup>
    import axios from "axios";
    import image from "../../../img/logo.png"
    import { onMounted, ref} from "vue"
    import { useRouter } from "vue-router";

    const router = useRouter();
    const hideContainer = ref(false);

    let form = ref({id: ''});
    
    const props = defineProps ({
        id: {
                type: String, 
                default: ''
        }
    })

    onMounted(async()=>{ 
        getInvoice();      
    })
    
    const getInvoice = async () => {
        let response = await axios.get(`/api/show_invoice/${props.id}`);      
         form.value = response.data.invoice;
    }

    const print = async () => { 
        hideContainer.value = true;
        await new Promise((resolve) => setTimeout(resolve, 100)); // Simulate async operation
        window.print();
        router.push('/').catch(() => {});
    }
     
    const onEdit = (id) => {        
        router.push('/invoice/edit/'+id); 
    }
   
    const deleteInvoice = (id) => {
        let text = "Are you sure you want to delete?";
        if (confirm(text) == true) {
            axios.get('/api/delete_invoice/'+id);
            router.push('/');
        }      
    }  

</script>
<template>
    <div class="container">
        <div class="invoices">
        
            <div class="card__header" :class="{ 'display-none': hideContainer }" >
                <div>
                    <h2 class="invoice__title">Invoice</h2>
                </div>            
            </div>

            <div :class="{ 'display-none': hideContainer }">
                <div class="card__header--title ">
                    <h1 class="mr-2">#{{form.id}}</h1>
                    <p>{{form.created_at}} </p>
                </div>
    
                <div>
                    <ul  class="card__header-list">
                        <li>
                            <!-- Select Btn Option -->
                            <button class="selectBtnFlat" @click="print()">
                                <i class="fas fa-print"></i>
                                Print
                            </button>
                            <!-- End Select Btn Option -->
                        </li>
                        <li>
                            <!-- Select Btn Option -->
                            <button class="selectBtnFlat" @click="onEdit(form.id)">
                             <i class=" fas fa-reply"></i>
                                Edit
                            </button>
                            <!-- End Select Btn Option -->
                        </li>
                        <li>
                            <!-- Select Btn Option -->
                            <button class="selectBtnFlat "  @click="deleteInvoice(form.id)">
                                <i class=" fas fa-pencil-alt"></i>
                                Delete
                            </button>
                            <!-- End Select Btn Option -->
                        </li>                    
                    </ul>
                </div>
            </div>

            <div class="table invoice">                
                <div class="invoice__header--title">
                    <p class="logo"><img :src="image" alt=""></p>
                    <p class="invoice__header--title-1">Invoice</p>                    
                </div>

            
                <div class="invoice__header--item">
                    <div>
                        <h2>Invoice To:</h2>
                        <p v-if="form.customer">{{form.customer.firstname}}</p>
                    </div>
                    <div>
                        <div class="invoice__header--item1">
                            <p>Invoice#</p>
                            <span>#{{ form.number }}</span>
                        </div>
                        <div class="invoice__header--item2">
                            <p>Date</p>
                            <span>{{ form.date }}</span>
                        </div>
                        <div class="invoice__header--item2">
                            <p>Due Date</p>
                            <span>{{ form.due_date }}</span>
                        </div>
                        <div class="invoice__header--item2">
                            <p>Reference</p>
                            <span>{{ form.reference }}</span>
                        </div>
                    
                    </div>
                </div>

                <div class="table py1">

                    <div class="table--heading3">
                        <p>#</p>
                        <p>Item Description</p>
                        <p>Unit Price</p>
                        <p>Qty</p>
                        <p>Total</p>
                    </div>
    
                    <!-- item 1 -->
                    <div class="table--items3" v-for="(item, i) in form.invoice_items" :key="item.id">
                        <p>{{i+1}}</p>
                        <p>{{item.product.description}}</p> 
                        <p>${{item.unit_price}}</p>
                        <p> {{item.quantity}}</p>
                        <p> ${{item.unit_price*item.quantity}}</p>
                    </div>                
                </div>

                <div  class="invoice__subtotal">
                    <div class="note">
                        <p>Thank you for your business</p>   
                    </div>
                    <div>
                        <div class="invoice__subtotal--item1">
                            <p>Sub Total</p>
                            <span> $ {{form.sub_total}}</span>
                        </div>
                        <div class="invoice__subtotal--item2">
                            <p>Discount</p>
                            <span>$ {{form.discount}}</span>
                        </div>
                        
                    </div>
                </div>

                <div class="invoice__total">
                    <div>
                        <h2>Terms and Conditions</h2>
                        <p>{{form.terms_and_conditions}}</p>
                    </div>
                    <div>
                        <div class="grand__total" >
                            <div class="grand__total--items">
                                <p>Total</p>
                                <span>${{form.total}}</span>
                            </div>
                        </div>
                    </div>
                </div>

            </div>                    
            
        </div>
    </div>
</template>

<style>
.display-none {
  display: none;
}
</style>