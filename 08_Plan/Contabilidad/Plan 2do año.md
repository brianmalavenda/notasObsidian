tipo::
tags::
fecha:: 2026-07-02
links::
links_externos::


## **FASE 1: Prueba de concepto personal

### Hardware inicial modesto:

- **1x RTX 3060 12GB** (~USD 350 usada)
    - Suficiente para DeepSeek-7B/GLM-4-9B cuantizados (Q4/Q5)
    - Procesa embeddings con modelos pequeños (BGE-small)
    - [Geforce RTX 3060 12GB](https://www.ebay.com/itm/278133600858?_skw=rtx+3060+12gb&itmmeta=01KWH2TWA3T7GZ6ZP27QJ7YZWK&hash=item40c20df25a%3Ag%3AVT0AAeSwyadqQPya&itmprp=enc%3AAQALAAAA8GfYFPkwiKCW4ZNSs2u11xDoVp2SIe1BdL60ks8sPFZk%2FQcxhcZc6%2BdhN3xZ6JR%2BMhjvkX8JoUZZynYBWAnoJEYhmggGYUn%2Fjez7Jb%2BBrwf6ahQ8S%2BvAJmJVDZlv2yiSwMq1a7WJnKBhrKUmbRrIcK%2BUpaPbLGL4DWkQNqH4ojCN6tTt7WLRvOaiWcgZAJq9cirKgsuBwbuSjPwVVfS4NpQUxPXxV3vFWbeZdWeo0KD7GRP33538cnGQbopurUedjvvPbzmCui%2B7XPr3ZGbvbZChZ1dTvutd8PI7sLzZJqJiIgyRkv7bq6NZvOiXtbTK%2FQ%3D%3D%7Ctkp%3ABk9SR5zF66LkZw) *USD 300* - **ebay**
- **CPU: Ryzen 7 7700X** (~USD 300)
    - 8 núcleos, suficiente para chunking y orquestación
    - [AMD Ryzen 7 5700X](https://www.aliexpress.com/p/trade/confirm.html?objectId=1005004127599184&from=aliexpress&countryCode=AR&shippingCompany=CAINIAO_STANDARD&provinceCode=&cityCode=&aeOrderFrom=main_detail&skuAttr=&skuId=12000058777564298&skucustomAttr=&quantity=1&spm=a2g0o.detail.0.0&curPageLogUid=1782985921963_YQMCYK) *USD 220* - **aliexpress**
    - [AMD Ryzen 7 7700X](https://www.ebay.com/itm/186670950693?_skw=ryzen+7+7700&epid=6057016923&itmmeta=01KWH3Z79KZQ6NSWS95MHSABRG&hash=item2b7674a925:g:4M8AAOSwA51m2SRH&itmprp=enc%3AAQALAAAA8GfYFPkwiKCW4ZNSs2u11xB6vADkUtA2EnOFFqnvMNtGDAdl9cyCuTY2ck08FXSxxhNlslT9vlj2N5Zq5XUhTokxkut5WYNVBNFU9Y%2B7ruCecwL44ng4%2F8mO8IuAlZVjohVisq6MY%2BZYB2XjwIvhso2c0f24As5w05NkcDU9xkVz3tRbgwuyBewyVcbYFv5Yk8SftISTLVj6XA2e%2BvXVgG9wTqH3orbXKf8ITWCh2Q%2B95moSoMc2FNVaaR%2B1Ur9UUSugLAahe9w%2FFQFFXigLsIRpXkTFriiMdos0OlTltvriSoIHC9STqB4%2FKxKKhzsgHw%3D%3D%7Ctkp%3ABk9SR5L1_KPkZw) *USD 290* - **ebay**
- **RAM: 32GB DDR5** (~USD 350)
    - Mínimo viable para 1GB de documentos con procesamiento secuencial
    - [Crucial DDR5 SDRAM](https://www.ebay.com/itm/318532117721?_skw=32+gb+ram+ddr5&epid=22052100521&itmmeta=01KWH3433EAG3SWR7QP9N3KEP7&hash=item4a29fe64d9:g:1PoAAeSwrBNqRflQ&itmprp=enc%3AAQALAAAA0GfYFPkwiKCW4ZNSs2u11xBTb6MLogwmIK8gG3uznvO75jTn2941%2Bp59PVdk5mhtabCSfXnAHwIoXCO%2FbJFpV%2FWeVa1%2BRlXY9xprE6vVoEAd1x%2FYAEUtUfruK10s5CZoTlZ2Oq1JEF7mNGFsxAYYa1OPodYObt2oNMN4PNRoRmqgSe6fU2qG3MicwznEAzUW3y6pXIaNzO%2FkaOnyhaaFe5do3%2FP%2Fm5L4GWaYqIs8iOb8aqqnKMsvNnGyTsPuQmVsydyLe8sYBtN%2FM426o4LdLWI%3D%7Ctkp%3ABk9SR_CxkKPkZw)  *USD 350* - **ebay**
    - [Corsair VENGEANCE (2x16GB)](https://www.ebay.com/itm/127945233937?_skw=32+gb+ram+ddr5&epid=6055646219&itmmeta=01KWH3433FA508TM67JKZYDH0N&hash=item1dca215611:g:jBcAAeSwEQFqQEL6&itmprp=enc%3AAQALAAAA0GfYFPkwiKCW4ZNSs2u11xA1bUwwq5XyzK%2FxpmkGTWrpLh%2Fc%2F1E7hzF7fqGkY9vfMdxMzGa%2BimyBKzlOZDKb5PZHKVOtzBhPBfyzod2YNMYt4wj72X369kpXSfRCPXV35dBLurPHz3ZO5em01q9kFrDzLNIxf1moRrZRC6itG6JQDx7ROYVpuFm%2FEm0mPVTmStGtt7asLta4yr25peXstNL%2BFSiV7kQ7P9255cUeBTSkLE85h%2FDOqtpKhkIWeXfNV47TG3SQHzUwTYa7S4M6tvY%3D%7Ctkp%3ABk9SR_KxkKPkZw) *USD 400* - **ebay**
    - [Team Group ELITE](https://www.ebay.com/itm/188588307579?_skw=32+gb+ram+ddr5&epid=21047883278&itmmeta=01KWH3433ECBFCT1N049D62XEM&hash=item2be8bd347b:g:ElsAAeSwvkBqRejU&itmprp=enc%3AAQALAAAA8GfYFPkwiKCW4ZNSs2u11xBriPDOCfC3m89RT7QVZk5ZxFYZY0czBb9%2FdPgPHTuO7Yw0vz2CdrxNQAGWR0%2B5qmKghzGOgOrdXkqVcQlrJbZjX%2FBE2q7hxt%2F8yzrvdTpZqHej%2BTXLekp43ZSze6NDMUHPz6pASafAKwi41yw08id92ZKkxRBTLyDUhZnTk0%2B8T%2Fd0bkCtiDFCS4%2F3mI6ytkVqxGMbR8IRRMClDLD%2FMAUxBHAPVTImBP1jYvaHg1TNXfCSyEDE9YilyVWCdSPXSxr4ZErjAQbvcdVy6iRaiErOG8cEUa3GmU6B4WST7VXjAA%3D%3D%7Ctkp%3ABk9SR_CxkKPkZw) *USD 330* - **ebay**
- **Almacenamiento: 1TB NVMe** (~USD 150)
    - ChromaDB o Qdrant local en disco
    - [SSD PUSKILL NVMe M.2 2280 1 TB](https://www.ebay.com/itm/188586022121?_skw=1tb+nvme&itmmeta=01KWH55RFSRH8N92NFQ12Z19PZ&hash=item2be89a54e9:g:jmEAAeSwak9qRUyf&itmprp=enc%3AAQALAAAA0GfYFPkwiKCW4ZNSs2u11xCogFN2JyMUfnjmXTODOq%2FYib4cU8n1QDdvZzlsUXEZINgSbXnAyOX487J912PDkvqamPdFdFD5syXiCVStdN57S3rLrXbWluTPGwA8CeTqZJ%2B3KbPOajXG09%2Fenuh%2Blw%2B6X4TraUVdj4tWtjhugP28qwVCaMpjfbey1K34%2BJJ0xlkGxchHp%2FMyTnx%2F3TVlV0Z1F9ccOJ8NAKwt3c5RbPoXiRHyCPOeTu3RndUCLVIQ4ZKmEEn9O8HiZlYdQw0Wpdo%3D%7Ctkp%3ABk9SR4qIl6XkZw) *USD 120* - **ebay**
- **Motherboard B650 + PSU 750W** (~USD 400)
	- [ASUS Prime B650-PLUS](https://www.ebay.com/itm/277835287329?_skw=motherboard+B650&itmmeta=01KWH587GHF2Y7DTKDGTR2D4N1&hash=item40b0460b21:g:u~MAAeSw2gBqIN3k&itmprp=enc%3AAQALAAAA8GfYFPkwiKCW4ZNSs2u11xBVYV4SStoDdwDMFF27ZSwYjgYiM0EomdmmzKbefVCTnBhdKxeKGy5vIxBbK125n%2BIP2zi%2FdpyGNCpIfAABeChDFpMXHAuEzb%2Fs5x%2BPRgoxlcHcv5vzJyi6bSpM%2BKjq3MQKhMs6e%2FCH4W3IAcSn2%2B52Nn2MMGDYEM%2BcE36jFwND9UqC9QsnSrjAUaM8JxTiemNb0UvLqwsi%2BCpeurN4ht1yNlOMh9GuTxll%2ByfnoYfgPDTjM%2BLkdKPhjLwl2j228Qv0DB8MylQL32tFW0iAs%2FD7DUTvVkoVjZykGTANm4CTbw%3D%3D%7Ctkp%3ABk9SR7b4oKXkZw) *USD 200* 
	- [ASUS TUF GAMING B650E-PLUS](https://www.ebay.com/itm/127949840474?_skw=motherboard+B650+ASUS+TAICHI&epid=11090502734&itmmeta=01KWH5FKXB09F21ZBYCDTK2F00&hash=item1dca67a05a:g:ZIgAAeSwy7BqQ-q~&itmprp=enc%3AAQALAAABAGfYFPkwiKCW4ZNSs2u11xDKo64%2Flp4kYbwv901rzltPuu6I2YGDh1EOaopyF%2FDHitH1YVzERToziR8z9iAUareXaeXg8TLJ%2BrkDSLLkdzB2PnkhYOcnqq3TXCIq65oA0symmjP4hqsNudzl38%2BPRHO2pY5V67jx4Nlum9CjwkHddncKBcQ3yFDVJQDEz6WPkXW7xGNctY3xBn2LqP7ITbuVV5fz6hQjjumxDUlU2VsRAv%2FsH8BIGDjzuP9%2FvWs%2FQnAwXzNtz1gpVS5N47wnmaoHpZVLA1ievSK3b6F9dL4z5rJ3VNs2w3kFC6Vjw3ezuzU7OD58AItj4xHIKa69DVo%3D%7Ctkp%3ABFBM6L6-peRn) *USD 220*
	- [PowerSpec 750 vatios 80 Plus Bronze ATX](https://www.ebay.com/itm/278109246574?_skw=psu+750w&itmmeta=01KWH5KA5PNFJ9YBF7KBDPQVPC&hash=item40c09a546e:g:Qa4AAeSwDDxqNu8D&itmprp=enc%3AAQALAAAAwGfYFPkwiKCW4ZNSs2u11xAd5WD5M9hqsXfX%2FOzp%2BAP59bgeIjvqieDTRqmGF0YGaEKgRsNHHz8LMfFHgGxbKDXmhqY5avTrOq1Ue7gfcE3Ruuob097KzXFaq63DR5ixelPVBuaFJMU8QSWs1gxl3bax%2FgX6EVUzKp%2BI6m5RP3nQGTRrE1eR46o6USKUy%2F1vkCcNj1ONJbb66cvi0q06fdcsOIk%2FGhaQLc6IKfwBUr7oH%2BVO9lqlzWcbzeFwI8xhqw%3D%3D%7Ctkp%3ABlBMUICjzaXkZw) *USD 150*
	- [DeepCool DQ750-M-V2L 750W 80 PLUS](https://www.ebay.com/itm/326833140970?_skw=psu+750w&itmmeta=01KWH5KA5PQ4XAYG67VA8ZPFDE&hash=item4c18c5f4ea:g:NfoAAeSwAIBo~BED&itmprp=enc%3AAQALAAAAwGfYFPkwiKCW4ZNSs2u11xCbiVPEqiy1vPIj6gzhaPjYa5JAT8T71euCTEx2KKE6xZMY3BQULu8Nn%2B9mIzkLSjSw%2FzvbL6EtPWOPkXIAzW%2F1kMIwYop8ZzRAOY4ZWoHM4bs%2FMcVxgU4%2ByBSVE5hrJGWkpDmHYZ%2FRDcu1AFBHt5KqEgJsm2GpH%2BeYPrKGkHyl8870bu6O9G7zBMrkOOzcxwiytqy21j837ZQm6vYmH3f0YXi6P6qQb7pOkvf4TQOxJg%3D%3D%7Ctkp%3ABlBMUIKjzaXkZw) USD 200

Falta gabinete, disipador -> USD 200

#### **Proyección USD 1.750**

**Limitaciones aceptables:**
- Modelos pequeños (7B-13B parámetros)
- Procesamiento secuencial de documentos (no paralelo)
- Solo 1-2 usuarios simultáneos
- Latencia: 8-15 segundos por consulta RAG

**Stack mínimo viable:**
- Ollama + Open WebUI para testing
- LangChain con ChromaDB (sin Docker, sin Kubernetes)
- Modelos: Mistral-7B, Phi-3-medium, Qwen-2.5-7B
    

---

## **FASE 2: Escalamiento intermedio

### Actualizaciones escalonadas:

**Paso 2.1 - Mejora GPU (+USD 1,500):**
- **RTX 3090 usada 24GB**
- Ahora puedes correr DeepSeek-33B Q4 o GLM-4-Chat
- Procesamiento paralelo de embeddings con BGE-large
- VRAM extra permite FAISS/Milvus en memoria
- [ZOTAC GAMING GeForce RTX 3090 24GB GDDR6X Trinity](https://www.ebay.com/itm/287421760061?_skw=RTX+3090&itmmeta=01KWH6A6BKNCBNJK4PDXE3A834&hash=item42ebac023d:g:9FgAAeSwXQNqP1fj&itmprp=enc%3AAQALAAAA4GfYFPkwiKCW4ZNSs2u11xDgwbnQspElqZfLePpZMYkGuR5BFtMdIj7o2%2FwYDTgZCFqj60cFtiBjH9tfEfX3j%2F7BoOUNEAuv7uwzJ57G4yli9g6OKnjJopkbrj496iorz0Plv%2BctbGnlRlK7xNJJPBigI2ZqQIGPjfYRqPXvU8sZsO08CjiLrGq7GU%2FxgPmZuVi%2BYx0zyKR18jecX%2FNo7ugnSBC1KuxyLgtQMeGEkcZ6HBlsoO0hDF%2BZChD4gsE%2B4hclFK6ecXGLaEKTQSHnUOB%2F1v7N8VtD1GnzhgBmytNS%7Ctkp%3ABk9SR_7lqKbkZw) *USD 1400* 

**Paso 2.2 - Duplica RAM (+USD 350):**
- **32GB → 64GB DDR5**
- Chunking más rápido, múltiples documentos simultáneos

**Paso 2.3 - Almacenamiento (+USD 150):**
- **Añade 2TB NVMe secundario** para vectores    
- Separa OS/modelos de datos vectoriales

### Nuevas capacidades:

- Modelos hasta 33B parámetros
- 3-5 usuarios simultáneos
- Procesamiento batch de documentos
- Latencia: 4-8 segundos

#### **Proyección USD 2.000**

---

## **FASE 3: Configuración óptima (~USD 3,000 adicionales)**

### Evolución final:

**Paso 3.1 - Dual GPU (+USD 2,000):**
- **Añade segunda RTX 3090** (total 48GB VRAM)
- Paralelismo real: una GPU para embeddings, otra para generación
- Posibilidad de servir 2 modelos simultáneos

**Paso 3.2 - CPU profesional (+USD 1100):**
- **Migra a Threadripper 5955WX** (si motherboard actual no soporta)
- 16 núcleos para procesamiento intensivo de documentos

**Paso 3.3 - RAM final (+USD 700):**
- **64GB → 128GB ECC**
- Seguridad de datos para clientes empresariales

**Paso 3.4 - Infraestructura (+USD 700):**
- PSU 1600W + refrigeración mejorada
- Chasis rackeable si ya tienes clientes

**Capacidad completa:**
- Modelos hasta 70B (DeepSeek-V2, Qwen-2.5-72B)
- 10+ usuarios simultáneos
- Alta disponibilidad con redundancia
- Latencia: <3 segundos

#### **Proyección USD 4.500**

INVERSION TOTAL ACUMULADA ESCALONADA USD 8.250 -> $ 12.375.000
	1 servidor dedicado a alto procesamiento, chatbot de IA en producción
	1 servidor dedicado a desarrollo con agentes de IA
	**acumulado de inversión anterior**
	1 servidor dedicado a almacenamiento en la nube
	1 servidor dedicado a backups
	1 nodo para administración de los servidores
INVERSION CON FASE 0 + FASE 3' USD 6.250 -> $ 9.375.000

---
#### Referencias

