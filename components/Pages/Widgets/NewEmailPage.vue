<script setup lang="ts">

import { h } from 'vue'
import { useForm } from 'vee-validate'
import { toTypedSchema } from '@vee-validate/zod'
import * as z from 'zod'
import {
  FormControl,
  FormField,
  FormItem,
  FormLabel,
} from '~/components/ui/form'
import { toast } from '~/components/ui/toast/use-toast'
import {authenticationStore} from "~/stores/authentication.store";

const registration = registrationStore()

const loadingStore = useLoadingStore();
const store = authenticationStore();
// const route = useRoute();
// const usr = userStore();
// usr.configure(parseInt(<string>route.query['RT']))

loadingStore.hide();

const formSchema = toTypedSchema(z.object({
  mail: z.string().email('*').min(5),
}))

const { isFieldDirty, handleSubmit } = useForm({
  validationSchema: formSchema,
})

const onSubmit = handleSubmit( async (values) => {
  loadingStore.show();
  try{
    await store.authenticate(values.mail)
    registration.next()
  }catch(e){
    loadingStore.hide();
    toast({
      title: 'You submitted the following values:',
      description: h('pre', { class: 'mt-2 w-[340px] rounded-md bg-slate-950 p-4' }, h('code', { class: 'text-white' }, JSON.stringify(e, null, 2))),
    })
  }
})

</script>
<template>
  <form class="space-y-6" @submit="onSubmit">
    <FormField v-slot="{ componentField }" name="mail" :validate-on-blur="!isFieldDirty">
      <FormItem>
        <FormLabel>Email</FormLabel>
        <FormControl v-bind="componentField">
          <CommonFormInput type="email" placeholder="Votre email" />
        </FormControl>
      </FormItem>
    </FormField>
    <CommonFormSubmit />
  </form>
  <Separator class="my-5" />
  <CommonAuth />
</template>
