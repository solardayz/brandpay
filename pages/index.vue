<template>
  <div class="container">
    <head>
      <meta charset="utf-8" />
      <link
        rel="icon"
        href="https://static.toss.im/icons/png/4x/icon-toss-logo.png"
      />
      <link rel="stylesheet" type="text/css" href="/style.css" />
      <meta http-equiv="X-UA-Compatible" content="IE=edge" />
      <meta name="viewport" content="width=device-width, initial-scale=1.0" />
      <title>토스페이먼츠 샘플 프로젝트</title>
      <!-- SDK 추가 -->
      <script src="https://js.tosspayments.com/v2/standard"></script>
    </head>
    <div>
      <b-navbar toggleable="lg" type="dark" variant="info">
        <b-navbar-brand href="#">NavBar</b-navbar-brand>

        <b-navbar-toggle target="nav-collapse"></b-navbar-toggle>

        <b-collapse id="nav-collapse" is-nav>
          <b-navbar-nav>
            <b-nav-item href="#">Link</b-nav-item>
            <b-nav-item href="#" disabled>Disabled</b-nav-item>
          </b-navbar-nav>

          <!-- Right aligned nav items -->
          <b-navbar-nav class="ml-auto">
            <b-nav-form>
              <b-form-input
                size="sm"
                class="mr-sm-2"
                placeholder="Search"
              ></b-form-input>
              <b-button size="sm" class="my-2 my-sm-0" type="submit"
                >Search</b-button
              >
            </b-nav-form>

            <b-nav-item-dropdown text="Lang" right>
              <b-dropdown-item href="#">EN</b-dropdown-item>
              <b-dropdown-item href="#">ES</b-dropdown-item>
              <b-dropdown-item href="#">RU</b-dropdown-item>
              <b-dropdown-item href="#">FA</b-dropdown-item>
            </b-nav-item-dropdown>

            <b-nav-item-dropdown right>
              <!-- Using 'button-content' slot -->
              <template #button-content>
                <em>User</em>
              </template>
              <b-dropdown-item href="#">Profile</b-dropdown-item>
              <b-dropdown-item href="#">Sign Out</b-dropdown-item>
            </b-nav-item-dropdown>
          </b-navbar-nav>
        </b-collapse>
      </b-navbar>
    </div>
    <div class="contaniner">
      <body>
        <!-- 주문서 영역 -->
        <div class="wrapper">
          <div
            class="box_section"
            style="
              padding: 40px 30px 50px 30px;
              margin-top: 30px;
              margin-bottom: 50px;
            "
          >
            <h1>일반 결제</h1>
            <!-- 결제 UI -->
            <div id="payment-method" style="display: flex">
              <button
                id="CARD"
                class="btn btn-primary"
                onclick="selectPaymentMethod('CARD')"
              >
                카드
              </button>
              <button
                id="TRANSFER"
                class="btn btn-primary"
                onclick="selectPaymentMethod('TRANSFER')"
              >
                계좌이체
              </button>
              <button
                id="VIRTUAL_ACCOUNT"
                class="btn btn-primary"
                onclick="selectPaymentMethod('VIRTUAL_ACCOUNT')"
              >
                가상계좌
              </button>
              <button
                id="MOBILE_PHONE"
                class="btn btn-primary"
                onclick="selectPaymentMethod('MOBILE_PHONE')"
              >
                휴대폰
              </button>
              <button
                id="CULTURE_GIFT_CERTIFICATE"
                class="btn btn-primary"
                onclick="selectPaymentMethod('CULTURE_GIFT_CERTIFICATE')"
              >
                문화상품권
              </button>
              <button
                id="FOREIGN_EASY_PAY"
                class="btn btn-primary"
                onclick="selectPaymentMethod('FOREIGN_EASY_PAY')"
              >
                해외간편결제
              </button>
            </div>
            <!-- 결제하기 버튼 -->
            <button
              class="btn btn-primary"
              style="margin-top: 30px"
              onclick="requestPayment()"
            >
              결제하기
            </button>
          </div>
          <div
            class="box_section"
            style="
              padding: 40px 30px 50px 30px;
              margin-top: 30px;
              margin-bottom: 50px;
            "
          >
            <h1>정기 결제</h1>
            <!-- 빌링 버튼 -->
            <button
              class="btn btn-primary"
              style="margin-top: 30px"
              onclick="requestBillingAuth()"
            >
              빌링키 발급하기
            </button>
          </div>
        </div>
        <script>
          const amount = {
            currency: 'KRW',
            value: 50000,
          }

          let selectedPaymentMethod = null

          function selectPaymentMethod(method) {
            if (selectedPaymentMethod != null) {
              document.getElementById(
                selectedPaymentMethod
              ).style.backgroundColor = '#ffffff'
            }

            selectedPaymentMethod = method

            document.getElementById(
              selectedPaymentMethod
            ).style.backgroundColor = 'rgb(229 239 255)'
          }

          // ------  SDK 초기화 ------
          // TODO: clientKey는 개발자센터의 API 개별 연동 키 > 결제창 연동에 사용하려할 MID > 클라이언트 키로 바꾸세요.
          // TODO: server.js 의 secretKey 또한 결제위젯 연동 키가 아닌 API 개별 연동 키의 시크릿 키로 변경해야 합니다.
          // TODO: 구매자의 고유 아이디를 불러와서 customerKey로 설정하세요. 이메일・전화번호와 같이 유추가 가능한 값은 안전하지 않습니다.
          // @docs https://docs.tosspayments.com/sdk/v2/js#토스페이먼츠-초기화
          const clientKey = 'test_ck_D5GePWvyJnrK0W0k6q8gLzN97Eoq'
          const customerKey = generateRandomString()
          const tossPayments = TossPayments(clientKey)
          // 회원 결제
          // @docs https://docs.tosspayments.com/sdk/v2/js#tosspaymentspayment
          const payment = tossPayments.payment({
            customerKey,
          })
          // 비회원 결제
          // const payment = tossPayments.payment({customerKey: TossPayments.ANONYMOUS});

          // ------ '결제하기' 버튼 누르면 결제창 띄우기 ------
          // @docs https://docs.tosspayments.com/sdk/v2/js#paymentrequestpayment
          async function requestPayment() {
            // 결제를 요청하기 전에 orderId, amount를 서버에 저장하세요.
            // 결제 과정에서 악의적으로 결제 금액이 바뀌는 것을 확인하는 용도입니다.
            switch (selectedPaymentMethod) {
              case 'CARD':
                await payment.requestPayment({
                  method: 'CARD', // 카드 및 간편결제
                  amount,
                  orderId: generateRandomString(),
                  orderName: '토스 티셔츠 외 2건',
                  successUrl: window.location.origin + '/payment/success.html', // 결제 요청이 성공하면 리다이렉트되는 URL
                  failUrl: window.location.origin + '/fail.html', // 결제 요청이 실패하면 리다이렉트되는 URL
                  customerEmail: 'customer123@gmail.com',
                  customerName: '김토스',
                  customerMobilePhone: '01012341234',
                  card: {
                    useEscrow: false,
                    flowMode: 'DEFAULT',
                    useCardPoint: false,
                    useAppCardOnly: false,
                  },
                })
              case 'TRANSFER':
                await payment.requestPayment({
                  method: 'TRANSFER', // 계좌이체 결제
                  amount,
                  orderId: generateRandomString(),
                  orderName: '토스 티셔츠 외 2건',
                  successUrl: window.location.origin + '/payment/success.html',
                  failUrl: window.location.origin + '/fail.html',
                  customerEmail: 'customer123@gmail.com',
                  customerName: '김토스',
                  customerMobilePhone: '01012341234',
                  transfer: {
                    cashReceipt: {
                      type: '소득공제',
                    },
                    useEscrow: false,
                  },
                })
              case 'VIRTUAL_ACCOUNT':
                await payment.requestPayment({
                  method: 'VIRTUAL_ACCOUNT', // 가상계좌 결제
                  amount,
                  orderId: generateRandomString(),
                  orderName: '토스 티셔츠 외 2건',
                  successUrl: window.location.origin + '/payment/success.html',
                  failUrl: window.location.origin + '/fail.html',
                  customerEmail: 'customer123@gmail.com',
                  customerName: '김토스',
                  customerMobilePhone: '01012341234',
                  virtualAccount: {
                    cashReceipt: {
                      type: '소득공제',
                    },
                    useEscrow: false,
                    validHours: 24,
                  },
                })
              case 'MOBILE_PHONE':
                await payment.requestPayment({
                  method: 'MOBILE_PHONE', // 휴대폰 결제
                  amount,
                  orderId: generateRandomString(),
                  orderName: '토스 티셔츠 외 2건',
                  successUrl: window.location.origin + '/payment/success.html',
                  failUrl: window.location.origin + '/fail.html',
                  customerEmail: 'customer123@gmail.com',
                  customerName: '김토스',
                  customerMobilePhone: '01012341234',
                })
              case 'CULTURE_GIFT_CERTIFICATE':
                await payment.requestPayment({
                  method: 'CULTURE_GIFT_CERTIFICATE', // 문화상품권 결제
                  amount,
                  orderId: generateRandomString(),
                  orderName: '토스 티셔츠 외 2건',
                  successUrl: window.location.origin + '/payment/success.html',
                  failUrl: window.location.origin + '/fail.html',
                  customerEmail: 'customer123@gmail.com',
                  customerName: '김토스',
                  customerMobilePhone: '01012341234',
                })
              case 'FOREIGN_EASY_PAY':
                await payment.requestPayment({
                  method: 'FOREIGN_EASY_PAY', // 해외 간편결제
                  amount: {
                    value: 100,
                    currency: 'USD',
                  },
                  orderId: generateRandomString(),
                  orderName: '토스 티셔츠 외 2건',
                  successUrl: window.location.origin + '/payment/success.html',
                  failUrl: window.location.origin + '/fail.html',
                  customerEmail: 'customer123@gmail.com',
                  customerName: '김토스',
                  customerMobilePhone: '01012341234',
                  foreignEasyPay: {
                    provider: 'PAYPAL',
                    country: 'KR',
                  },
                })
            }
          }

          async function requestBillingAuth() {
            await payment.requestBillingAuth({
              method: 'CARD', // 자동결제(빌링)은 카드만 지원합니다
              successUrl: window.location.origin + '/payment/billing.html', // 요청이 성공하면 리다이렉트되는 URL
              failUrl: window.location.origin + '/fail.html',
              customerEmail: 'customer123@gmail.com', // 요청이 실패하면 리다이렉트되는 URL
              customerName: '김토스',
            })
          }

          function generateRandomString() {
            return window.btoa(Math.random()).slice(0, 20)
          }
        </script>
      </body>
    </div>

    <footer
      class="d-flex flex-wrap justify-content-between align-items-center py-3 my-4 border-top"
    >
      <div class="col-md-4 d-flex align-items-center">
        <a
          href="/"
          class="mb-3 me-2 mb-md-0 text-body-secondary text-decoration-none lh-1"
        >
          <svg class="bi" width="30" height="24">
            <use xlink:href="#bootstrap" />
          </svg>
        </a>
        <span class="mb-3 mb-md-0 text-body-secondary"
          >&copy; 2024 Company, Inc</span
        >
      </div>

      <ul class="nav col-md-4 justify-content-end list-unstyled d-flex">
        <li class="ms-3">
          <a class="text-body-secondary" href="#"
            ><svg class="bi" width="24" height="24">
              <use xlink:href="#twitter" /></svg
          ></a>
        </li>
        <li class="ms-3">
          <a class="text-body-secondary" href="#"
            ><svg class="bi" width="24" height="24">
              <use xlink:href="#instagram" /></svg
          ></a>
        </li>
        <li class="ms-3">
          <a class="text-body-secondary" href="#"
            ><svg class="bi" width="24" height="24">
              <use xlink:href="#facebook" /></svg
          ></a>
        </li>
      </ul>
    </footer>
  </div>
</template>

<script>
export default {
  data() {
    return {
      products: [
        { id: 1, name: '상품1', price: 10000 },
        { id: 2, name: '상품2', price: 20000 },
        { id: 3, name: '상품3', price: 30000 },
        { id: 4, name: '상품4', price: 40000 },
        { id: 5, name: '상품5', price: 50000 },
      ],
      priceRanges: [
        '10000원 ~ 20000원',
        '20000원 ~ 30000원',
        '30000원 ~ 40000원',
        '40000원 ~ 50000원',
      ],
      selectedPriceRange: '전체 상품',
      filteredProducts: this.products,
    }
  },
  methods: {
    filterProductsByPriceRange(priceRange) {
      this.selectedPriceRange = priceRange
      this.filteredProducts = this.products.filter((product) => {
        const minPrice = priceRange.split(' ~ ')[0].replace(/원/, '')
        const maxPrice = priceRange.split(' ~ ')[1].replace(/원/, '')
        return product.price >= minPrice && product.price <= maxPrice
      })
    },
  },
}
</script>
