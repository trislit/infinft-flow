<template>
  <div class="mintedInformation" :class="statusModalMode">
    <h3>{{ mintStatusTitle }}</h3>
    <div id="completedMintContent">
      <p>
        Your tokens are minted and can be seen using the view page, or on
        Opensea.
      </p>
      <div>
        <nuxt-link :to="`/gallery/${activeContractId}`">View Gallery</nuxt-link>
        <nuxt-link
          :to="`/view/${activeContractId}/${mintedData && mintedData.tokenId}`"
          >View Token</nuxt-link
        >
      </div>
    </div>
    <div v-if="fakeMintedData" class="help">
      <div>Contract: {{ fakeMintedData.contractId }}</div>
      <div>Token: {{ fakeMintedData.tokenId }}</div>
      <div>Network: {{ fakeMintedData.network }}</div>
    </div>

    <div class="transactionInfo" v-if="openseaLink">
      <label class="mintTransactionLabel">OPENSEA</label>
      <div>
        <a :href="openseaLink" class="etherscanLink" target="blank">
          <IconExternalLink strokeClass="dark" />
          {{ openseaLink }}
        </a>
      </div>
    </div>
    <div class="transactionInfo" v-if="etherscanLink">
      <label class="mintTransactionLabel">Transaction</label>
      <div>
        <a :href="etherscanLink" class="etherscanLink" target="blank">
          <IconExternalLink
            :strokeClass="statusModalMode === 'fixed' ? 'dark' : 'dark'"
          />
          {{ etherscanLink }}
        </a>
      </div>
    </div>

    <div class="actions">
      <button
        id="resetMintButton"
        class="w3-button w3-block w3-padding-large w3-black w3-margin-bottom"
        @click="resetMintForm"
      >
        Reset
      </button>
      <button
        class="btn hollow"
        @click="handleCompletedModal"
        style="margin-top: 0.5rem;"
      >
        <IconContract strokeClass="dark" />
        <span>minimise dialog</span>
      </button>
      <button class="btn hollow" @click="setMintStatus('ready')">
        <span>set ready</span>
      </button>
    </div>
  </div>
</template>

<style>
.mintedInformation {
  position: relative;
  background: var(--fill-color, #111);
  color: var(--text-color, #111);
  border-top: 1px solid var(--ui-color, #111);
}
.mintedInformation {
  padding: 0.5rem;
}
.mintedInformation.inline {
  padding: 1rem;
  position: fixed;
  left: 0;
  bottom: 0;
  right: 0;
  z-index: 999;
}
.mintedInformation.fixed .closeButtonWrap {
  position: absolute;
  top: 0.5rem;
  right: 0.5rem;
}
.mintedInformation.inline .closeButtonWrap {
  position: absolute;
  top: 0rem;
  right: 0rem;
}
.closeButtonWrap button {
  transform: scale(1);
}
.closeButtonWrap button:hover {
  transform: scale(1.1);
}
.statusInformation h3 {
  margin: 0;
}
.statusRow {
  display: flex;
  flex-direction: row;
  align-items: center;
  justify-content: center;
}
.statusRow .statusIcon {
  flex-basis: 2rem;
  height: 2rem;
  display: flex;
  align-items: center;
  justify-content: center;
}
.transactionInfo {
  border-top: 1px solid var(--line-color, #ccc);
}
.etherscanLink {
  display: inline-flex;
  justify-content: flex-start;
  align-items: center;
  font-size: 0.75rem;
}
</style>
<script>
import { mapMutations, mapGetters, mapActions } from 'vuex'
export default {
  name: 'MintedInformation',
  data() {
    return {
      fakeMintedData: {
        contractId: '0xC85BDEd5D6AbF3051B096CCCd30F1C73a31bE274',
        network: 'rinkeby',
        tokenId: 21,
      },
    }
  },
  computed: {
    ...mapGetters({
      walletNetwork: 'ui/walletNetwork',
      activeContractId: 'ui/activeContractId',
      statusModalMode: 'ui/statusModalMode',
      mintTransactionId: 'mintFormStore/mintTransactionId',
      mintStatus: 'mintFormStore/mintStatus',
      mintStatusMessage: 'mintFormStore/mintStatusMessage',
      mintStatusTitle: 'mintFormStore/mintStatusTitle',
      mintedData: 'mintFormStore/mintedData',
    }),
    openseaLink() {
      console.log('walletNetwork', this.walletNetwork)
      console.log('mintedData', this.mintedData)
      const data = this.mintedData || this.fakeMintedData
      console.log('data', data)
      if (!data) {
        return null
      }
      const { tokenId = 999 } = data

      const link =
        data.network === 'rinkeby'
          ? `https://rinkeby.opensea.io/assets/${data.contractId}/${data.tokenId}`
          : `https://opensea.io/assets/${data.contractId}/${data.tokenId}`
      return link
    },
    etherscanLink() {
      console.log('walletNetwork', this.walletNetwork)
      console.log('mintTransactionId', this.mintTransactionId)
      if (!this.mintTransactionId) {
        return null
      }
      const link =
        this.walletNetwork === 'rinkeby'
          ? `https://rinkeby.etherscan.io/tx/${this.mintTransactionId}`
          : `https://etherscan.io/tx/${this.mintTransactionId}`
      return link
    },
  },
  methods: {
    ...mapMutations({
      setMintStatus: 'mintFormStore/setMintStatus',
      resetMintForm: 'mintFormStore/resetMintForm',
      setStatusModalMode: 'ui/setStatusModalMode',
    }),
    ...mapActions({
      showStatusModal: 'mintFormStore/showStatusModal',
    }),
    handleResetMintForm() {
      alert('todo')
    },
    handleStatusModal(newState) {
      if (newState) {
        // this.$store.commit('mintFormStore/setShowStatusModal', newState)
        this.$store.commit('ui/setStatusModalMode', 'fixed')
        // this.$modal.show('status-modal')
      } else {
        // this.$store.commit('mintFormStore/setShowStatusModal', newState)
        this.$store.commit('ui/setStatusModalMode', 'inline')
        // this.$modal.hide('status-modal')
      }
    },
    handleCompletedModal(newState) {
      alert('todo!')
      if (newState) {
        // this.$store.commit('ui/setStatusModalMode', 'fixed')
      } else {
        // this.$store.commit('ui/setStatusModalMode', 'inline')
      }
    },

    setMintStatus(status) {
      this.$store.commit('mintFormStore/setMintStatus', status)
    },
  },
}
</script>