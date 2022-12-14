<template>
  <div>
    <div class="my-4 mt-10 space-y-4">
        <div class="grid sm:grid-cols-2 md:grid-cols-4 gap-5">
          <div>
            <AppFormLabel>Data da transação</AppFormLabel>
            <AppFormInput v-model="localTransaction.date" type="date" />
          </div>

          <div>
            <AppFormLabel>Valor</AppFormLabel>
            <AppFormInput v-model="localTransaction.amount" type="number" />
          </div>

          <div>
            <AppFormLabel>Descrição</AppFormLabel>
            <AppFormInput v-model="localTransaction.description" />
          </div>

          <div>
            <AppFormLabel>Categoria</AppFormLabel>
            <AppFormSelect 
              v-model="localTransaction.categoryId" 
              :options="categories" 
            />
          </div>
        </div>

        <div class="space-x-4 flex items-center justify-end">
          <a
              href="#"
              class="text-red-600 hover:text-red-900"
              @click.stop.prevent="deleteTransaction(localTransaction.id)"
            >
              Excluir
            </a>

          <a 
            href="" 
            class="inline-flex text-gray-700 text-sm"
            @click.prevent.stop="onCancel()"
          >
            Cancelar
          </a>

          <AppButton @click="updateTransaction">
            Editar
          </AppButton>
        </div>
      </div>
  </div>
</template>

<script>
import AppButton from '~/components/Ui/AppButton';
import AppFormInput from '~/components/Ui/AppFormInput';
import AppFormLabel from '~/components/Ui/AppFormLabel';
import AppFormSelect from '~/components/Ui/AppFormSelect';

export default {
  name: 'TransactionEdit',

  components: {
    AppButton,
    AppFormInput,
    AppFormLabel,
    AppFormSelect,
  },

  props: {
    transaction: {
      type: Object,
      default: () => ({}),
    },
  },

  data() {
    return {
      localTransaction: {
        id: this.transaction.id,
        date: this.transaction.date,
        description: this.transaction.description,
        amount: this.transaction.amount,
        categoryId: this.transaction.category.id,
      },
      categories: [],
    };
  },

  async fetch() {
    this.categories = await this.$store.dispatch('categories/getCategories');
  },

  methods: {
    updateTransaction() {
      this.$store.dispatch('transactions/updateTransaction', { id: this.transaction.id, data: this.localTransaction})
      .then((response) => {
        this.$emit('update', {
          ...response,
          category: this.categories.find(o => o.id == this.localTransaction.categoryId),
        });
        this.onCancel();
      });
    },
    onCancel() {
      this.$emit('cancel');
    },
    deleteTransaction(id) {
      this.$store.dispatch('transactions/deleteTransaction', { id })
      .then(() => {
        this.$emit('delete', this.localTransaction);
        this.onCancel();
      });
    }
  },
}
</script>