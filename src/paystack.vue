<script lang="ts">
import {defineComponent, PropType, h, isVue2} from "vue-demi"

interface MetaData {
  [key: string]: any
}

export default defineComponent({
  props: {
    embed: {
      type: Boolean as PropType<boolean>,
      default: true,
    },
    paystackKey: {
      type: String as PropType<string>,
      required: true,
    },
    email: {
      type: String as PropType<string>,
      required: true,
    },
    firstname: {
      type: String as PropType<string>,
      required: true,
    },
    lastname: {
      type: String as PropType<string>,
      required: true,
    },
    amount: {
      type: Number as PropType<number>,
      required: true,
    },
    reference: {
      type: String as PropType<string>,
      required: true,
    },
    channels: {
      type: Array as PropType<string[]>,
      default: () => ["card", "bank"],
    },
    callback: {
      type: Function as PropType<(response: any) => void>,
      required: true,
    },
    close: {
      type: Function as PropType<() => void>,
      required: true,
    },
    metadata: {
      type: Object as PropType<MetaData>,
      default: () => {},
    },
    currency: {
      type: String as PropType<string>,
      default: "",
    },
    plan: {
      type: String as PropType<string>,
      default: "",
    },
    quantity: {
      type: String as PropType<string>,
      default: "",
    },
    subaccount: {
      type: String as PropType<string>,
      default: "",
    },
    splitCode: {
      type: String as PropType<string>,
      default: "",
    },
    transactionCharge: {
      type: Number as PropType<number>,
      default: 0,
    },
    bearer: {
      type: String as PropType<string>,
      default: "",
    },
  },
  data() {
    return {
      scriptLoaded: false,
    }
  },
  created() {
    this.loadScript()
  },
  methods: {
    async loadScript(): Promise<void> {
      const scriptPromise = new Promise<boolean>((resolve) => {
        const script: any = document.createElement("script")
        script.defer = true
        script.src = "https://js.paystack.co/v1/inline.js"

        // Add script to document head
        document.getElementsByTagName("head")[0].appendChild(script)

        if (script.readyState) {
          // IE support
          script.onreadystatechange = () => {
            if (script.readyState === "complete") {
              script.onreadystatechange = null
              resolve(true)
            }
          }
        } else {
          // Others
          script.onload = () => {
            resolve(true)
          }
        }
      })

      this.scriptLoaded = await scriptPromise

      if (this.scriptLoaded && this.embed) {
        this.payWithPaystack()
      }
    },
    payWithPaystack(): void {
      if (this.scriptLoaded) {
        const paystackOptions = {
          key: this.paystackKey,
          email: this.email,
          firstname: this.firstname,
          lastname: this.lastname,
          channels: this.channels,
          amount: this.amount,
          ref: this.reference,
          callback: (response: any) => {
            this.callback(response)
          },
          onClose: () => {
            this.close()
          },
          metadata: this.metadata,
          currency: this.currency,
          plan: this.plan,
          quantity: this.quantity,
          subaccount: this.subaccount,
          split_code: this.splitCode,
          transaction_charge: this.transactionCharge,
          bearer: this.bearer,
        }
        const windowEl: any = window
        const handler = windowEl.PaystackPop.setup(paystackOptions)

        handler.openIframe()
      }
    },
  },
  render() {
    if (isVue2) {
      return h(
        "button",
        {
          staticClass: ["paystack-button"],
          style: [{display: !this.embed ? "block" : "none"}],
          attrs: {type: "button"},
          on: {click: this.payWithPaystack},
        },
        this.$slots.default ? this.$slots.default : "PROCEED TO PAYMENT"
      )
    }

    return h(
      "button",
      {
        class: ["paystack-button"],
        style: [{display: !this.embed ? "block" : "none"}],
        type: "button",
        onClick: this.payWithPaystack,
      },
      this.$slots.default ? this.$slots.default() : "PROCEED TO PAYMENT"
    )
  },
})
</script>
