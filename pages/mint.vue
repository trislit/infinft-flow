<template>
  <div class="pageContainer">
    <Header />
    <!--  VUE MINT FORM SECTION-->
    <section id="mint" class="mint markerNot borderBottom">
      <div class="tertiary">
        <h2>Mint Tokens</h2>
        <div class="aside">
          <!-- <p>Mint NFT tokens using your deployed custom contract.</p> -->
          <p>
            Use the contract id generated in the deploy form to create your own tokens.
          </p>
          <p>
            File size is currently limited to 500Mb.
          </p>
          <p>
            Batch minting (editions) occurs automatically up to the Total Editions entered.
            A maximum of 10 tokens can be batch minted at one time.
          </p>
        </div>
      </div>
      <div class="primary">
        <client-only>
          <transition name="fade" appear>
            <div v-if="!walletAddress" class="connectPanel shadow">
              <div class="connectHeader">
                <div class="connectUserIcon shadow">
                <IconUser size="large" :strokeClass="contrastMode" />
                </div>
              </div>
              <div class="connectBody">
                <h3>Not Connected</h3>
                <p >Connect your Ethereum Wallet to use InfiNFT minting</p>
                <Button
                  fill
                  size="large"
                  mode="primary"
                  @click="
                    connectWallet({
                      setWallet,
                      setWalletStatus,
                      setWalletChain,
                      setNetworkName,
                      setEnsName,
                    })
                  "
                >
                  Connect
                </Button>
              </div>
            </div>
          </transition>
          <transition name="fade" appear>
            <div class="shadow contractStatusPanel" v-if="walletAddress && deployStatus === 'completed'">
              <div v-if="devMode">status: {{deployStatus}}</div>
              <DeployStatusInformation v-if="walletAddress && deployStatus === 'completed'" />
            </div>
          </transition>
          <!-- <transition name="fade" appear>
            <div class="shadow contractStatusPanel" v-if="walletAddress && deployStatus === 'completed'">
              <div v-if="devMode">status: {{deployStatus}}</div>
              <DeployStatusInformation v-if="walletAddress && deployStatus === 'completed'" />
            </div>
          </transition> -->
          
          <transition name="fade" appear>
            <div v-if="walletAddress && !activeContractId" class="connectPanel shadow">
              <div class="connectSelect row">
                <div class="column" :class="selectedConnectPanel === 'existing' ? 'selected' : 'notselected' "
                  @click="setSelectedPanel('existing')"
                >
                  <h4>Existing Contract</h4>
                  <p class="small">Use Existing Minting Contract</p>
                </div>
                <div class="column" :class="selectedConnectPanel === 'new' ? 'selected' : 'notselected' "
                  @click="setSelectedPanel('new')"
                >
                  <h4>New Contract</h4>
                  <p class="small">Generate New Minting Contract</p>
                </div>
              </div>
              <div class="connectBody"  v-if="selectedConnectPanel === 'existing'">
                <LoadContractForm />
              </div>
              <div class="connectBody" v-if="selectedConnectPanel === 'new'">
                  
                <div v-if="walletAddress" class="row NewFormRow">
                  <DeployForm displayMode="inline" />
                </div>
              </div>
            </div>
          </transition>

        
          <div v-if="walletAddress" class="row">
            <div class="contractSection column" style="flex-basis: 100%">
              <div
                class="fieldset formContent"
                id="fieldsetContractView"
                v-if="activeContractId && deployStatus !== 'completed'"
              >
                <!-- v-if="!showEditContract" -->
                <label>Active Contract </label>
                <div class="row">
                  <div class="column col-66">
                    <div id="userContractAddress">{{ activeContractId }}</div>
                  </div>
                  <div class="column col-33">
                    <Button
                      mode="hollow"
                      size="small"
                      @click="goToGallery(activeContractId)"
                    >
                      View Gallery
                    </Button>
                    <button
                      class="btn"
                      id="editUserContractButton"
                      @click="handleAccountModal(true)"
                    >
                      <IconEdit strokeClass="light" size="small" />
                    </button>
                  </div>
                </div>
              </div>
            </div>
          </div>
          <div v-if="walletAddress && deployStatus !== 'completed'" class="row">
              
            <MintForm />
            <MintPreview />
          </div>
        </client-only>
      </div>
      <div class="secondary">
        <div class="aside help">
          <!-- <h6>Minting NFT Tokens</h6> -->
          <!-- <p>
            Use the contract id generated in the deploy form to create your own tokens.
          </p>
          <p>
            File size is currently limited to 500Mb.
          </p>
          <p>
            Batch minting (editions) occurs automatically up to the Total Editions entered.
            A maximum of 10 tokens can be batch minted at one time.
          </p> -->
        </div>
      </div>
    </section>

    <!--  VUE DEPLOY FORM SECTION-->
    <client-only>
      <section id="deploy" class="deploy market borderBottom" v-if="1===2">
        <div class="tertiary">
          <h2>Artist Contract</h2>
          <div class="aside">
            <p>Your own mint contract to be used to generate tokens.</p>
          </div>
        </div>
        <div class="primary">
          <div v-if="!walletAddress" class="noAccess">
            <h4>No Wallet found.</h4>
            <p>Connect an ethereum wallet to mint tokens</p>
            <Button
              size="large"
              mode="primary"
              @click="
                connectWallet({
                  setWallet,
                  setWalletStatus,
                  setWalletChain,
                  setNetworkName,
                  setEnsName,
                })
              "
            >
              Connect
            </Button>
          </div>
          <div v-if="walletAddress" class="row">
            <DeployForm  />
          </div>
        </div>
        <div class="secondary">
          <div class="aside help">
            <h6>User Contract</h6>
            <p>
              Your contract works similarly to 'SuperRare', 'Known Origin', and
              similar marketplaces.
            </p>
            <p>Creating a contract is free, but you must pay a gas fee.</p>
          </div>
        </div>
      </section>
    </client-only>
    <Footer />
  </div>
</template>

<style lang="scss">
.contractStatusPanel{
  border: 1px solid var(--ui-color, #eee);
  padding: 0rem 1rem 1rem;
  margin-bottom: 50px;
  background: var(--fill-color, #ccc);
}
.connectPanel{
  border: 1px solid var(--ui-color, #eee);
  padding: 0rem 1rem 1rem;
  margin-top: 50px;
  background: var(--fill-color, #ccc);
  
  .connectHeader{
    display: flex;
    align-items: center;
    justify-content: center;
    flex-direction: row;
    position: relative;
    margin-top: -50px;
    .connectUserIcon{
      display: flex;
      align-items: center;
      justify-content: center;
      flex-direction: row;
      width: 100px;
      height: 100px;
      border-radius: 100px;
      border: 1px solid var(--ui-color, #eee);
      background: var(--fill-color, #eee);
    }
  }
  .connectBody{
    display: flex;
    flex-direction: column;
    align-items: center;
    justify-content: center;
  }
  .connectSelect{
    margin-left: -1rem;;
    margin-right: -1rem;
    > .column{
      padding: .25rem;
      display: flex;
      align-items: center;
      flex-direction: column;
      border-bottom: 1px solid var(--ui-color);
      cursor: pointer;
      p, h4{
        margin: 0;
      }
      &:nth-child(2){
        border-left: 1px solid var(--ui-color);
      }
      &.selected{
        border-bottom: 1px solid transparent;
      }
      &:not(.selected){
        background: rgba(0,0,0,.05);
      }
    }
    + .connectBody{
      padding: 1rem 0;
      align-items: flex-start;
      justify-content: flex-start;
      .NewFormRow{
        width: 100%;
        flex-basis: 100%;
      }
    }
  }
}
</style>

<script>
import { mapMutations, mapGetters, mapActions } from "vuex";
import { connectWallet, handleAccountLink } from "../utils/wallet";
const BASE_URL = process.env.tempUrl || "https://infinft.app"
import ogImagePreview from '~/assets/images/preview.jpg'

export default {
  name: "MintPage",
   head: {
    title: 'InfiNFT: Mint Tokens',
    meta: [
      { hid: "og:title", name: "og:title", content: "InfiNFT: Mint NFT Tokens with on-chain meta" },
      {
        hid: 'og:description',
        name: 'og:description',
        content: 'Mint your own custom NFT tokens'
      },
      {
          hid: "og:image",
          property: "og:image",
          content: BASE_URL + ogImagePreview
      },
    ],
   },
   data(){
     return {
       selectedConnectPanel: 'existing'
     }
   },
   mounted() {
     // console.log(this.usedContracts)
     if(this.usedContracts && this.usedContracts.length){
       this.setSelectedPanel('existing')
     } else {
       this.setSelectedPanel('new')
     }
   },
  computed: {
    ...mapGetters({
      devMode: "ui/devMode",
      hasWallet: "ui/hasWallet",
      walletAddress: "ui/walletAddress",
      activeContractId: "ui/activeContractId",
      contrastMode: "ui/contrastMode",
      usedContracts: "ui/usedContracts",
      showEditContract: "mintFormStore/showEditContract",
      deployStatus: "deployFormStore/deployStatus"
    }),
  },
  methods: {
    connectWallet,
    ...mapMutations({
      setShowAccount: "ui/setShowAccount",
      setShowSearch: "ui/setShowSearch",
      setWallet: "ui/setWallet",
      setEnsName: "ui/setEnsName",
      setWalletStatus: "ui/setWalletStatus",
      setNetworkName: "ui/setNetworkName",
      setWalletChain: "ui/setWalletChain",
    }),
    setSelectedPanel(value){
      if(!value){
        return
      }
      this.selectedConnectPanel = value
    },
    handleAccountModal() {
      this.$modal.show("account-modal");
    },
    goToGallery(contractId){
      this.$router.push({
        path: `/gallery/${contractId}`,
      })
    }
  },
};
</script>
