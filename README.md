# Chainlink Geliştirici Bootcamp - CELO ile Özel Konuk Oturumu

Bu depo, [Chainlink Geliştirici Bootcamp 2024](https://lu.ma/ChainlinkBootcamp2024?utm_source=0czoelvgwhbx) için yapılan sunumun bir parçasıdır.

Sunu buradan bulabilirsiniz:


## Celo-Composer'ı Yükleyin

Birkaç dakika içinde bir dApp kurmanıza yardımcı olan [Celo-Composer](https://github.com/celo-org/celo-composer)'ı kullanacağız. Bu, hackathonlar için mükemmel veya yeni bir prototipi test etmek istiyorsanız idealdir.

Celo Composer ile başlamanın en kolay yolu, [`@celo/celo-composer`](https://github.com/celo-org/celo-composer) kullanmaktır. Bu CLI aracı, React (ya react-celo ya da rainbowkit-celo ile) dahil olmak üzere birden fazla çerçeve üzerinde Celo'da dApp'ler oluşturmanıza olanak tanır. Başlamak için aşağıdaki komutu çalıştırın ve adımları izleyin:

```bash
npx @celo/celo-composer@latest create
```

### Tüm Bağımlılıkları Yükleyin

Projeyi sevdiğiniz kod düzenleyicide açın.

Yeni bir terminal açın ve şunu çalıştırın:


```bash
npm i
```

veya 

```bash
yarn
```

şimdi ön ucu çalıştırmak için, komutu `package.json` dosyasında betikler altında bulabilirsiniz:

```bash
npm run react-app:dev
```

Ön uç şu adreste çalışmalıdır: `http://localhost:3000`.

## Jeton Bakiyesini Alın

CELO jetonlarının bakiyesini okumak için önce bir miktar almanız gerekecek, bu yüzden [Celo Musluğu'na](https://faucet.celo.org/alfajores) gidin ve cüzdanınıza bir miktar alın. Geliştirme için özel bir cüzdan kullanmanızı her zaman öneririz, gerçek fon tutmayan bir cüzdan.

`react-app` klasöründeki `index.tsx` dosyasında bazı şablon kodlar bulacaksınız. Kodumuzu buraya ekleyeceğiz.

İlk olarak, CELO jetonlarının bakiyesini okumak istiyoruz.


1. Bunun için Alfajores'deki CELO adresini almalıyız. Adresleri [Celo belgelerinde](https://docs.celo.org/token-addresses) bulabilirsiniz. Veya [celoscan](https://celoscan.io/tokens)'deki en iyi ERC20 jetonlarını kontrol ederken bulabilirsiniz.

```typescript
    const CELOTokenAddress = "0xF194afDf50B03e69Bd7D057c1Aa9e10c9954E4C9"; // CELO Testnet

```

2. ERC20 abi'sini viem'den alacağız.
```typescript
 //  Viem'den bir ERC20 abi'sini alın
    import { erc20Abi } from "viem";
```

3. Sözleşmelerden okuma yapmak için [web3.js](https://web3js.org/) kullanacağız, bu yüzden bir örnek oluşturmalıyız ve Celo Alfajores için RPC ile başlatmalıyız.

Web3.js'i Yükleyin

```bash
yarn add web3
```

Web3.js'i İçe Aktarın


```typescript
 
```

Web3 örneğini oluşturun. RPC'yi bulabilirsiniz [Celo belgelerinde](https://docs.celo.org/network)'de

```typescript
    import Web3 from "web3";
```


4. Sözleşmeyle etkileşimde bulunmak için celo sözleşmesinin bir örneğini oluşturun
```typescript
    const celoContract = new web3.eth.Contract(erc20Abi, CELOTokenAddress)
```

5. Kullanıcının mevcut CELO bakiyesini okumak için sözleşme işlevini çağırın.

```typescript
    // bakiye değerini durumda sakla
    const [celoBalance, setCeloBalance] = useState("");

    const getBalance = async () => {
        celoContract.methods.balanceOf(userAddress as `0x${string}`).call()
            .then(balance => {
                // Bakiye Wei olarak döner, Ether'a (veya jetonun eşdeğeri) dönüştürün
                const tokenBalance = web3.utils.fromWei(balance, 'ether');
                setCeloBalance((tokenBalance))
                console.log(`Bakiye: ${tokenBalance}`);
            })
            .catch(error => {
                console.error(error);
            });

        return;
    };
```

6. React hook'unda fonksiyonu çağırın. Hook zaten var ve kodumuzu ekliyoruz

```typescript
    useEffect(() => {
        if (isConnected && address) {
            setUserAddress(address);

            // bu kodu ekleyin, çünkü yalnızca bağlı olduğumuzda ve bileşen oluşturulduğunda kullanıcı bakiyesini yüklemek istiyoruz.
            if (isMounted) {
                getBalance();
            }
        }
        // isMounted buraya da eklenmelidir
    }, [address, isConnected, isMounted]);
```

7. Kullanıcının mevcut CELO jetonlarının miktarını göstermek için HTML'ye bazı kodlar ekleyin

```typescript
    return (
        <div className="flex flex-col justify-center items-center">
            <div className="h1">
                İşte... Bir sonraki Celo projeniz için bir tuval!
            </div>
            {isConnected ? (
                <div className="h2 text-center">
                    Adresiniz: {userAddress}

                    {/* değerlerinizi buraya ekleyin, onları iki ondalık değere kısaltacağız */}
                    <p> CELO Bakiye {Number(celoBalance).toFixed(2)}</p>
                
                </div>

            ) : (
                <div>Cüzdan Bağlı Değil</div>
            )}
        </div>
    );
```


Kodunuz şimdi şö

yle görünmelidir: 

```typescript
// ERC20 abi 

export default function Home() {
    //  Viem'den bir ERC20 abi'sini alın
    import { erc20Abi } from "viem";

    // CELO jeton adresi Alfajore Celo Testnet'te
    const CELOTokenAddress = "0xF194afDf50B03e69Bd7D057c1Aa9e10c9954E4C9"; 

    const web3 = new Web3("https://alfajores-forno.celo-testnet.org")
    const celoContract = new web3.eth.Contract(erc20Abi, CELOTokenAddress)

    const [userAddress, setUserAddress] = useState("");
    const [isMounted, setIsMounted] = useState(false);
    // celoBalance için bir state oluşturun
    const [celoBalance, setCeloBalance ]  = useState("");

      useEffect(() => {
        setIsMounted(true);
    }, []);

    useEffect(() => {
        if (isConnected && address) {
            setUserAddress(address);

            if (isMounted) {
                getBalance();
            }
        }
    }, [address, isConnected, isMounted]);

    if (!isMounted) {
        return null;
    }

    const getBalance = async () => {
          celoContract.methods.balanceOf(userAddress as `0x${string}`).call()
            .then(balance => {
                // Bakiye Wei olarak döner, Ether'a (veya jetonun eşdeğeri) dönüştürün
                const tokenBalance = web3.utils.fromWei(balance, 'ether');
                setCeloBalance((tokenBalance))
                console.log(`Bakiye: ${tokenBalance}`);
            })
            .catch(error => {
                console.error(error);
            });

        return;
    };

        return (
        <div className="flex flex-col justify-center items-center">
            <div className="h1">
                İşte... Bir sonraki Celo projeniz için bir tuval!
            </div>
            {isConnected ? (
                <div className="h2 text-center">
                    Adresiniz: {userAddress}
                    {/* değerlerinizi buraya ekleyin, onları iki ondalık değere kısaltacağız */}
                    <p> CELO Bakiye {Number(celoBalance).toFixed(2)}</p>
                </div>

            ) : (
                <div>Cüzdan Bağlı Değil</div>
            )}
        </div>
    );
}
```

## CELO jetonlarının USD değerini alın

Bu öğretici için [Chainlink belgelerindeki](https://docs.chain.link/data-feeds/using-data-feeds#reading-data-feeds-offchain) veri fiyatı beslemelerini okuma kılavuzunu takip edeceğiz.


1. CELO/USD fiyat beslemesinin adresini [Chainlink belgelerinde](https://docs.chain.link/data-feeds/price-feeds/addresses?network=celo&page=1#overview) bulun

```typescript
    // Alfajores'deki CELO/USD fiyat besleme adresi
    const celoToUsd = "0x022F9dCC73C5Fb43F2b4eF2EF9ad3eDD1D853946";
```

2. Eğitimdeki aggregatorV3InterfaceABI'yi ekleyin 
```typescript
    // Alfajores'deki CELO/USD fiyat besleme adresi
    const aggregatorV3InterfaceABI = [
    {
        inputs: [],
        name: "decimals",
        outputs: [{ internalType: "uint8", name: "", type: "uint8" }],
        stateMutability: "view",
        type: "function",
    },
    {
        inputs: [],
        name: "description",
        outputs: [{ internalType: "string", name: "", type: "string" }],
        stateMutability: "view",
        type: "function",
    },
    {
        inputs: [{ internalType: "uint80", name: "_roundId", type: "uint80" }],
        name: "getRoundData",
        outputs: [
        { internalType: "uint80", name: "roundId", type: "uint80" },
        { internalType: "int256", name: "answer", type: "int256" },
        { internalType: "uint256", name: "startedAt", type: "uint256" },
        { internalType: "uint256", name: "updatedAt", type: "uint256" },
        { internalType: "uint80", name: "answeredInRound", type: "uint80" },
        ],
        stateMutability: "view",
        type: "function",
    },
    {
        inputs: [],
        name: "latestRoundData",
        outputs: [
        { internalType: "uint80", name: "roundId", type: "uint80" },
        { internalType: "int256", name: "answer", type: "int256" },
        { internalType: "uint256", name: "startedAt", type: "uint256" },
        { internalType: "uint256", name: "updatedAt", type: "uint256" },
        { internalType: "uint80", name: "answeredInRound", type: "uint80" },
        ],
        stateMutability: "view",
        type: "function",
    },
    {
        inputs: [],
        name: "version",
        outputs: [{ internalType: "uint256", name: "", type: "uint256" }],
        stateMutability: "view",
        type: "function",
    },
    ]
```

3. Fiyat besleme sözleşmesi için sözleşme örneğini oluşturun

```typescript
    // fiyat besleme sözleşmesinin sözleşme örneği
    const priceFeed = new web3.eth.Contract(aggregatorV3InterfaceABI, celoToUsd)
```

4. Son fiyat verilerini almak için latestRound verilerini çağırın. Bu, aldığınız yanıt verisidir, bu yüzden ikinci değeri okumak isteyeceksiniz
```typescript
    0: roundID,
    1: answer, // bu istediğimiz değer
    3: timeStamp,
    2: startedAt,
    4: answeredInRound
```

İlk olarak, celoValue'yu saklamak için state ekleyelim

```typescript
   // celoValue'yu state içinde saklayın
    const [celoValue, setCeloValue] = useState("");
```

Sonra fiyat besleme verilerini çağırmak için fonksiyonu ekleyin


```typescript
    const getUSDValue = async () => {
    priceFeed.methods
        .latestRoundData()
        .call()
       

 .then((roundData: any) => {
            // Yanıt nesnesinin birinci konumundan değeri alın. Değer büyükInt olarak gelecek, bu yüzden onu biçimlendirmemiz gerekecek. 
            const balance = (formatUnits(roundData[1], 8))
            setCeloValue(balance)
            // roundData ile bir şey yapın
            console.log("Son Round Verisi", roundData)
        })
    };

```

Takma kulaklığımızda, dApp bir cüzdanla bağlandığında ve bileşen bağlandığında işlevi çağırın

```typescript
    useEffect(() => {
        if (isConnected && address) {
            setUserAddress(address);
            if (isMounted) {
                getBalance();
                getUSDValue()
            }
        }
    }, [address, isConnected, isMounted]);
```

Kullanıcıya celoValue'yu göstermek için bazı kod ekleyin

```typescript
    return (
        <div className="flex flex-col justify-center items-center">
            <div className="h1">
                İşte... Bir sonraki Celo projeniz için bir tuval!
            </div>
            {isConnected ? (
                <div className="h2 text-center">
                    Adresiniz: {userAddress}
                     {/* değerlerinizi buraya ekleyin, onları iki ondalık değere kısaltacağız */}
                    <p> CELO Bakiye {Number(celoBalance).toFixed(2)}</p>
                    <p> CELO'nun USD değeri {(Number(celoBalance) * Number(celoValue)).toFixed(2)}</p>
                </div>
            ) : (
                <div>No Wallet Connected</div>
            )}
        </div>
    );
```

Şimdi kodunuz şuna benzer görünmelidir


```typescript
export default function Home() {
    const celoToUsd = "0x022F9dCC73C5Fb43F2b4eF2EF9ad3eDD1D853946"; // Fiyat Besleme Sözleşme Adresi. Burada bulabilirsiniz: https://docs.chain.link/data-feeds/price-feeds/addresses?network=celo&page=1#overview

    const [celoValue, setCeloValue ]  = useState("");

   const getUSDValue = async () => {
        priceFeed.methods
            .latestRoundData()
            .call()
            .then((roundData: any) => {
                const balance = (formatUnits(roundData[1], 8))
                setCeloValue(balance)
                // roundData ile bir şey yapın
                console.log("Son Round Verisi", roundData)
            })
    };
}
```

## Son kod

Kullanıcıya değerleri göstermek için bazı kod ekleyelim. Artık işlemimiz tamamlandı. Tebrikler. Artık dApp'inize fiyat besleme verilerini nasıl entegre edeceğinizi biliyorsunuz.

```typescript
import { useEffect, useState } from "react";
import { useAccount } from "wagmi";
//  Viem'den bir ERC20 abi'sini alın
import { erc20Abi, formatUnits } from "viem";
import Web3 from "web3";

export default function Home() {
    const CELOTokenAddress = "0xF194afDf50B03e69Bd7D057c1Aa9e10c9954E4C9"; // CELO Testnet
    // Alfajores'deki CELO/USD fiyat besleme adresi
    const celoToUsd = "0x022F9dCC73C5Fb43F2b4eF2EF9ad3eDD1D853946";
    // create a Web3 instance, initialize it with the RPC for Celo Alfajores
    // fiyat besleme sözleşmesinin sözleşme örneği
    const aggregatorV3InterfaceABI = [
        {
            inputs: [],
            name: "decimals",
            outputs: [{ internalType: "uint8", name: "", type: "uint8" }],
            stateMutability: "view",
            type: "function",
        },
        {
            inputs: [],
            name: "description",
            outputs: [{ internalType: "string", name: "", type: "string" }],
            stateMutability: "view",
            type: "function",
        },
        {
            inputs: [{ internalType: "uint80", name: "_roundId", type: "uint80" }],
            name: "getRoundData",
            outputs: [
                { internalType: "uint80", name: "roundId", type: "uint80" },
                { internalType: "int256", name: "answer", type: "int256" },
                { internalType: "uint256", name: "startedAt", type: "uint256" },
                { internalType: "uint256", name: "updatedAt", type: "uint256" },
                { internalType: "uint80", name: "answeredInRound", type: "uint80" },
            ],
            stateMutability: "view",
            type: "function",
        },
        {
            inputs: [],
            name: "latestRoundData",
            outputs: [
                { internalType: "uint80", name: "roundId", type: "uint80" },
                { internalType: "int256", name: "answer", type: "int256" },
                { internalType: "uint256", name: "startedAt", type: "uint256" },
                { internalType: "uint256", name: "updatedAt", type: "uint256" },
                { internalType: "uint80", name: "answeredInRound", type: "uint80" },
            ],
            stateMutability: "view",
            type: "function",
        },
        {
            inputs: [],
            name: "version",
            outputs: [{ internalType: "uint256", name: "", type: "uint256" }],
            stateMutability: "view",
            type: "function",
        },
    ]
    const web3 = new Web3("https://alfajores-forno.celo-testnet.org")
    const celoContract = new web3.eth.Contract(erc20Abi, CELOTokenAddress)
    // fiyat besleme sözleşmesinin sözleşme örneği
    const priceFeed = new web3.eth.Contract(aggregatorV3InterfaceABI, celoToUsd)


    const [userAddress, setUserAddress] = useState("");
    const [isMounted, setIsMounted] = useState(false);
    // celoBalance için bir state oluşturun
    const [celoBalance, setCeloBalance] = useState("");
    // celoValue'yu state içinde saklayın
    const [celoValue, setCelo

Value] = useState("");


    useEffect(() => {
        setIsMounted(true);
    }, []);

    useEffect(() => {
        if (isConnected && address) {
            setUserAddress(address);

            if (isMounted) {
                getBalance();
                getUSDValue()
            }
        }
    }, [address, isConnected, isMounted]);

    if (!isMounted) {
        return null;
    }

    const getBalance = async () => {
        celoContract.methods.balanceOf(userAddress as `0x${string}`).call()
            .then(balance => {
                // Bakiye Wei olarak döner, Ether'a (veya jetonun eşdeğeri) dönüştürün
                const tokenBalance = web3.utils.fromWei(balance, 'ether');
                setCeloBalance((tokenBalance))
                console.log(`Bakiye: ${tokenBalance}`);
            })
            .catch(error => {
                console.error(error);
            });

        return;
    };

    const getUSDValue = async () => {
        priceFeed.methods
            .latestRoundData()
            .call()
            .then((roundData: any) => {
                const balance = (formatUnits(roundData[1], 8))
                setCeloValue(balance)
                // roundData ile bir şey yapın
                console.log("Son Round Verisi", roundData)
            })
    };


    return (
        <div className="flex flex-col justify-center items-center">
            <div className="h1">
                İşte... Bir sonraki Celo projeniz için bir tuval!
            </div>
            {isConnected ? (
                <div className="h2 text-center">
                    Adresiniz: {userAddress}
                    {/* değerlerinizi buraya ekleyin, onları iki ondalık değere kısaltacağız */}
                    <p> CELO Bakiye {Number(celoBalance).toFixed(2)}</p>
                    <p> CELO'nun USD değeri {(Number(celoBalance) * Number(celoValue)).toFixed(2)}</p>
                </div>
            ) : (
                <div>No Wallet Connected</div>
            )}
        </div>
    );
}

```
Başka sorularınız var mı?