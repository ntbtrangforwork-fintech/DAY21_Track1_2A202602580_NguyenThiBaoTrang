# Lab 21 — Phân tích rủi ro AI qua case study thực tế

- **Họ và tên:** Nguyễn Thị Bảo Trang
- **MSSV / mã học viên:** 2A202602580
- **Lớp:** Track 1
- **Ngành đã chọn:** Y tế / hệ thống AI hỗ trợ quyết định lâm sàng và sàng lọc sức khỏe

> **Phạm vi bài làm:** Bài phân tích ba hệ thống thuật toán/AI đã được nghiên cứu trong bối cảnh y tế thực tế. Các nhãn Low/Medium/High/Critical là đánh giá định tính phục vụ bài tập, không phải kết luận pháp lý hoặc chẩn đoán y khoa.

## 1. Industry Risk Snapshot

| Nội dung | Đánh giá của tôi và lý do |
| --- | --- |
| Những tác hại chính có thể xảy ra | Bỏ sót hoặc trì hoãn chẩn đoán/điều trị; đưa ra cảnh báo sai gây can thiệp không cần thiết; phân bổ nguồn lực y tế bất công; làm tăng gánh nặng cảnh báo cho nhân viên y tế; xâm phạm quyền riêng tư. Người chịu ảnh hưởng trực tiếp là bệnh nhân, đặc biệt các nhóm vốn khó tiếp cận dịch vụ y tế; bác sĩ, điều dưỡng và cơ sở y tế cũng chịu rủi ro vận hành và trách nhiệm chuyên môn. |
| Mức độ high-stakes | **Cao.** Kết quả của hệ thống có thể ảnh hưởng đến việc một người có được đưa vào chương trình chăm sóc, được cảnh báo sớm, được chuyển khám chuyên khoa hoặc được điều trị đúng lúc hay không. Sai sót có thể ảnh hưởng trực tiếp đến sức khỏe, thị lực, cơ hội tiếp cận chăm sóc và trong trường hợp nặng là tính mạng. |
| Dữ liệu nhạy cảm có thể được sử dụng | Hồ sơ bệnh án điện tử, chẩn đoán, thuốc, kết quả xét nghiệm, dấu hiệu sinh tồn, chi phí khám chữa bệnh, ảnh y khoa, tuổi, giới, chủng tộc/dân tộc và thông tin định danh. Đây là dữ liệu sức khỏe nhạy cảm; bài này chỉ sử dụng số liệu tổng hợp đã công bố, không đưa dữ liệu bệnh nhân thật vào repo. |
| Nhu cầu human review | **Cao.** Bác sĩ hoặc chuyên gia phù hợp phải xem lại trước khi AI làm thay đổi chẩn đoán, điều trị hay quyền tiếp cận dịch vụ. Cơ sở y tế cần kiểm định mô hình trên dữ liệu địa phương trước triển khai; theo dõi false negative, false positive, tỷ lệ ảnh không đọc được và hiệu năng theo nhóm; đồng thời phải có đường chuyển tuyến hoặc xử lý thủ công khi AI không chắc chắn. |

### Nguyên tắc kiểm soát đề xuất ở mức ngành

1. Không dùng đầu ra AI như quyết định cuối cùng trong các tình huống ảnh hưởng trực tiếp đến điều trị hoặc quyền tiếp cận chăm sóc.
2. Kiểm định ngoài tại từng cơ sở và theo dõi hiệu năng sau triển khai thay vì chỉ dựa vào kết quả do nhà phát triển công bố.
3. Đánh giá theo nhóm dân số để phát hiện chênh lệch về tỷ lệ bỏ sót, cảnh báo sai và khả năng tiếp cận.
4. Thiết kế cơ chế chuyển cho người có chuyên môn khi dữ liệu đầu vào kém, mô hình không chắc chắn hoặc kết quả mâu thuẫn với đánh giá lâm sàng.
5. Giảm thiểu dữ liệu, phân quyền truy cập, ghi nhật ký và công khai mục đích sử dụng dữ liệu cho bệnh nhân.

---

## 2. Case study 1 — Thuật toán quản lý sức khỏe quần thể tạo chênh lệch chủng tộc

### Brief Case

- **Tổ chức / sản phẩm AI:** Một thuật toán thương mại dùng trong quản lý sức khỏe quần thể tại Hoa Kỳ. Bài báo khoa học không công bố tên nhà cung cấp nên bài làm không tự gán tên sản phẩm.
- **Thời gian, địa điểm / bối cảnh:** Nghiên cứu công bố ngày 25/10/2019 trên *Science*, phân tích dữ liệu của một hệ thống y tế lớn tại Hoa Kỳ.
- **AI được dùng để làm gì:** Dự đoán chi phí y tế tương lai để xếp điểm rủi ro và chọn bệnh nhân vào chương trình quản lý/chăm sóc tăng cường.
- **Vấn đề hoặc sự kiện đáng chú ý:** Ở cùng một mức điểm rủi ro, bệnh nhân da đen có tình trạng bệnh nặng hơn bệnh nhân da trắng. Nguyên nhân được nhóm nghiên cứu xác định là mô hình dùng **chi phí y tế** làm đại diện cho **nhu cầu sức khỏe**. Do bất bình đẳng tiếp cận dịch vụ, chi phí thấp hơn không đồng nghĩa với ít bệnh hơn.
- **Số liệu có nguồn:** Nhóm nghiên cứu ước tính nếu khắc phục chênh lệch này, tỷ lệ bệnh nhân da đen trong nhóm được nhận hỗ trợ bổ sung sẽ tăng từ **17,7% lên 46,5%**. Phần tóm tắt của bài báo cho biết loại thuật toán này ảnh hưởng đến hàng triệu bệnh nhân.
- **Nguồn:** Ziad Obermeyer, Brian Powers, Christine Vogeli và Sendhil Mullainathan, “Dissecting racial bias in an algorithm used to manage the health of populations,” *Science*, 25/10/2019, DOI: [10.1126/science.aax2342](https://doi.org/10.1126/science.aax2342), đặc biệt phần Abstract và nội dung về proxy cost/health need.
- **Phân biệt bằng chứng và nhận định:** **Nguồn xác nhận** chênh lệch 17,7% so với 46,5%, tình trạng bệnh nặng hơn ở cùng điểm rủi ro và việc dùng chi phí làm biến mục tiêu. **Nhận định của tôi** là đây là allocational harm nghiêm trọng vì một thước đo tưởng như trung lập đã chuyển bất bình đẳng xã hội có sẵn vào quyết định phân bổ chăm sóc. Nghiên cứu chứng minh chênh lệch ở quy mô nhóm; nó không ghi nhận thương tổn y khoa cụ thể của từng cá nhân.

### Harm Map Worksheet

| Trường | Phân tích của tôi |
| --- | --- |
| High-risk moment | Khi điểm rủi ro được dùng để quyết định bệnh nhân nào được mời vào chương trình chăm sóc tăng cường. |
| Stakeholder bị ảnh hưởng | Bệnh nhân da đen là nhóm chịu ảnh hưởng trực tiếp; ngoài ra còn có bệnh nhân khác, bác sĩ, nhóm quản lý chăm sóc, bệnh viện, nhà bảo hiểm và nhà cung cấp thuật toán. |
| Failure mode | **Proxy/measurement bias và allocational bias:** mô hình tối ưu dự đoán chi phí thay vì nhu cầu sức khỏe, nên phân bổ thiếu hỗ trợ cho nhóm có chi tiêu y tế thấp vì rào cản tiếp cận. |
| Layer bắt đầu lỗi | **Grounding.** Biến mục tiêu “chi phí” không đại diện đầy đủ cho khái niệm cần ra quyết định là “mức độ cần chăm sóc”. Lỗi sau đó được mô hình hóa và đưa vào quy trình phân bổ. |
| Harm xảy ra là gì? | **Đã được quan sát ở mức hệ thống:** bệnh nhân da đen bị đại diện thấp trong nhóm nhận hỗ trợ dù có mức bệnh nặng hơn tại cùng điểm. **Chưa được nguồn xác nhận:** số ca biến chứng hoặc tổn thương lâm sàng trực tiếp do quyết định này. |
| Harm lens | Công bằng và không phân biệt đối xử; khả năng tiếp cận dịch vụ; sức khỏe và phúc lợi; trách nhiệm giải trình. |
| Severity | **High.** Quyết định ảnh hưởng đến quyền tiếp cận nguồn lực y tế; hậu quả lâm sàng cụ thể chưa được lượng hóa trong nguồn. |
| Scale | **High.** Nguồn mô tả thuật toán thuộc loại được dùng rộng rãi và ảnh hưởng đến hàng triệu bệnh nhân; kết quả định lượng được rút ra từ một hệ thống y tế cụ thể. |
| Probability | **High trong bối cảnh nghiên cứu.** Chênh lệch được đo trực tiếp và có tính hệ thống, không chỉ là một tình huống giả định. Không nên tự động suy rộng cùng tỷ lệ sang mọi hệ thống khác. |
| Frequency | **Lặp lại theo mỗi chu kỳ chấm điểm/chọn bệnh nhân.** Nếu proxy không đổi, sai lệch có thể tái diễn mỗi khi hệ thống phân bổ chương trình chăm sóc. |
| Vì sao? | Severity và scale cao vì quyết định phân bổ tác động đến nhiều người và liên quan trực tiếp đến chăm sóc sức khỏe. Bằng chứng mạnh cho chênh lệch về lựa chọn, nhưng còn giới hạn ở việc bài báo không định lượng tổn thương lâm sàng cuối cùng cho từng bệnh nhân. |

### Kiểm soát phù hợp cho case 1

- Dùng mục tiêu gần với nhu cầu lâm sàng, ví dụ gánh nặng bệnh tật hoặc khả năng hưởng lợi từ can thiệp, thay vì chỉ dùng chi phí.
- Đánh giá calibration, tỷ lệ được chọn và kết quả sau can thiệp theo nhóm dân số.
- Cho nhóm lâm sàng xem xét ngoại lệ và có cơ chế đưa bệnh nhân vào chương trình ngoài điểm số tự động.
- Công bố model card, mục đích sử dụng, giới hạn và quy trình khiếu nại/đánh giá lại.

---

## 3. Case study 2 — Epic Sepsis Model hoạt động kém trong kiểm định ngoài

### Brief Case

- **Tổ chức / sản phẩm AI:** Epic Systems — Epic Sepsis Model (ESM), mô hình dự đoán nhiễm trùng huyết tích hợp trong hồ sơ bệnh án điện tử.
- **Thời gian, địa điểm / bối cảnh:** Nghiên cứu hồi cứu tại Michigan Medicine, Hoa Kỳ, trên các ca nhập viện từ 06/12/2018 đến 20/10/2019; công bố trực tuyến ngày 21/06/2021 trên *JAMA Internal Medicine*.
- **AI được dùng để làm gì:** Tính điểm mỗi 15 phút để dự đoán nhiễm trùng huyết và hỗ trợ cảnh báo sớm cho nhân viên y tế.
- **Vấn đề hoặc sự kiện đáng chú ý:** Khi kiểm định ngoài tại ngưỡng điểm từ 6 trở lên, mô hình có khả năng phân biệt và calibration kém hơn mức từng được nhà phát triển báo cáo. Nhiều ca nhiễm trùng huyết không đạt ngưỡng, trong khi số ca bị gắn cờ đủ lớn để tạo nguy cơ alert fatigue.
- **Số liệu có nguồn:** Nghiên cứu gồm **27.697 bệnh nhân với 38.455 lượt nhập viện**; nhiễm trùng huyết xảy ra trong **2.552 lượt (7%)**. Ở ngưỡng 6, sensitivity là **33%**, positive predictive value là **12%** và AUC là **0,63**. Mô hình không nhận diện **1.709/2.552 ca nhiễm trùng huyết (67%)**, đồng thời đạt ngưỡng cảnh báo ở **6.971/38.455 lượt nhập viện (18%)**.
- **Nguồn:** Andrew Wong và cộng sự, “External Validation of a Widely Implemented Proprietary Sepsis Prediction Model in Hospitalized Patients,” *JAMA Internal Medicine*, 21/06/2021, DOI: [10.1001/jamainternmed.2021.2626](https://doi.org/10.1001/jamainternmed.2021.2626), xem Abstract, Results và Discussion.
- **Phân biệt bằng chứng và nhận định:** **Nguồn xác nhận** các chỉ số hiệu năng, số ca bị bỏ sót theo ngưỡng và nguy cơ gánh nặng cảnh báo. **Điểm giới hạn rất quan trọng:** đây là phân tích hồi cứu; nghiên cứu ghi rõ không tạo cảnh báo thật cho các bệnh nhân trong đánh giá. Vì vậy, “trì hoãn điều trị” hoặc “tử vong do ESM” chỉ là **nguy cơ có thể xảy ra**, không phải hậu quả đã được nghiên cứu này chứng minh.

### Harm Map Worksheet

| Trường | Phân tích của tôi |
| --- | --- |
| High-risk moment | Khi nhân viên y tế dựa vào điểm ESM để ưu tiên đánh giá một bệnh nhân hoặc bị ảnh hưởng bởi số lượng cảnh báo lớn trong ca trực. |
| Stakeholder bị ảnh hưởng | Bệnh nhân có hoặc có nguy cơ nhiễm trùng huyết; bác sĩ, điều dưỡng và đội phản ứng nhanh; bệnh viện; Epic; cơ quan quản lý chất lượng. |
| Failure mode | False negative/bỏ sót ca bệnh, calibration kém, positive predictive value thấp và alert fatigue do nhiều cảnh báo không dẫn đến một ca nhiễm trùng huyết. |
| Layer bắt đầu lỗi | **Model.** Hiệu năng dự đoán tại cơ sở kiểm định thấp. **Safety/UX** của ngưỡng và cơ chế cảnh báo có thể khuếch đại hậu quả bằng cách tạo quá nhiều cảnh báo hoặc khiến người dùng tin quá mức vào điểm số. |
| Harm xảy ra là gì? | **Đã xảy ra trong dữ liệu đánh giá:** mô hình không nhận diện 67% lượt nhập viện có nhiễm trùng huyết ở ngưỡng nghiên cứu và gắn cờ 18% tổng lượt nhập viện. **Chỉ là nguy cơ, chưa được nghiên cứu này chứng minh:** bệnh nhân bị chậm kháng sinh, diễn biến nặng hoặc tử vong do đầu ra ESM. |
| Harm lens | An toàn bệnh nhân; chất lượng và độ tin cậy; human factors/alert fatigue; minh bạch của mô hình độc quyền; trách nhiệm giải trình. |
| Severity | **Critical nếu một ca nhiễm trùng huyết thật bị bỏ sót và điều trị chậm.** Tuy nhiên, mức Critical ở đây là đánh giá hậu quả tiềm tàng; nghiên cứu không quy kết ca tổn thương cụ thể cho cảnh báo ESM. |
| Scale | **High về khả năng lan rộng.** Nguồn cho biết ESM đã được triển khai ở hàng trăm bệnh viện Hoa Kỳ; số liệu định lượng cụ thể chỉ đến từ một trung tâm với 38.455 lượt nhập viện. |
| Probability | **High đối với failure mode trong mẫu nghiên cứu:** 67% ca nhiễm trùng huyết không đạt ngưỡng trước thời điểm đánh giá. Xác suất gây tổn thương thực tế chưa được nghiên cứu này đo. |
| Frequency | Điểm được tính **mỗi 15 phút**; tại ngưỡng nghiên cứu, 18% lượt nhập viện từng bị gắn cờ. Do đó lỗi hoặc gánh nặng cảnh báo có thể lặp thường xuyên trong vận hành. |
| Vì sao? | Nhiễm trùng huyết cần phát hiện và xử lý sớm nên hậu quả tiềm tàng rất nghiêm trọng. Tuy vậy, đánh giá đạo đức phải giữ ranh giới bằng chứng: nghiên cứu đo hiệu năng dự đoán hồi cứu, không phải thử nghiệm chứng minh ESM trực tiếp gây hại. |

### Kiểm soát phù hợp cho case 2

- Bắt buộc kiểm định ngoài và hiệu chỉnh ngưỡng bằng dữ liệu địa phương trước khi bật cảnh báo.
- Không dùng “không cảnh báo” để loại trừ nhiễm trùng huyết; bác sĩ vẫn đánh giá theo triệu chứng và xét nghiệm.
- Theo dõi sensitivity, PPV, thời gian báo trước, số cảnh báo mỗi ca trực và phản hồi của nhân viên y tế.
- Có dashboard hậu kiểm, quy trình tạm dừng mô hình và đánh giá phiên bản khi hiệu năng suy giảm.
- Cung cấp thông tin đủ rõ về dữ liệu phát triển, phiên bản và giới hạn của mô hình độc quyền.

---

## 4. Case study 3 — AI sàng lọc bệnh võng mạc đái tháo đường tại Thái Lan

### Brief Case

- **Tổ chức / sản phẩm AI:** Google Health và các đối tác y tế Thái Lan — hệ thống deep learning đọc ảnh đáy mắt để sàng lọc bệnh võng mạc đái tháo đường.
- **Thời gian, địa điểm / bối cảnh:** Nghiên cứu đoàn hệ can thiệp tiến cứu tại 9 cơ sở chăm sóc ban đầu thuộc chương trình sàng lọc quốc gia Thái Lan, từ 12/12/2018 đến 29/03/2020; công bố ngày 07/03/2022 trên *The Lancet Digital Health*.
- **AI được dùng để làm gì:** Phân tích ảnh đáy mắt theo thời gian thực và đưa ra khuyến nghị chuyển khám đối với bệnh võng mạc đái tháo đường, phù hoàng điểm, ảnh không thể chấm hoặc thị lực thấp.
- **Vấn đề hoặc sự kiện đáng chú ý:** Hệ thống đạt hiệu năng cao nhưng vẫn có sai số và phụ thuộc vào chất lượng ảnh/quy trình tại cơ sở. Nghiên cứu đã dùng chuyên gia võng mạc đọc lại từng ảnh như một cơ chế an toàn. Điều này cho thấy một mô hình có accuracy cao vẫn cần human review và đường xử lý trường hợp không chắc chắn.
- **Số liệu có nguồn:** Có **7.940 bệnh nhân** được sàng lọc, **7.651 người (96,3%)** đủ điều kiện phân tích và **2.412 người (31,5%)** được chuyển khám vì một trong các lý do gồm bệnh võng mạc, phù hoàng điểm, ảnh không đọc được hoặc thị lực thấp. Đối với bệnh võng mạc đe dọa thị lực, hệ thống có accuracy **94,7%**, sensitivity **91,4%** và specificity **95,4%**.
- **Nguồn:** Paisan Ruamviboonsuk và cộng sự, “Real-time diabetic retinopathy screening by deep learning in a multisite national screening programme: a prospective interventional cohort study,” *The Lancet Digital Health*, 07/03/2022, DOI: [10.1016/S2589-7500(22)00017-6](https://doi.org/10.1016/S2589-7500(22)00017-6); bản tóm tắt tại [PubMed PMID 35272972](https://pubmed.ncbi.nlm.nih.gov/35272972/).
- **Phân biệt bằng chứng và nhận định:** **Nguồn xác nhận** quy mô nghiên cứu, tỷ lệ chuyển khám, các chỉ số hiệu năng và việc chuyên gia đọc lại ảnh. Từ sensitivity 91,4%, **tôi suy luận** rằng vẫn tồn tại tỷ lệ false negative trong nhóm thật sự có bệnh; phép suy luận này không cho biết một bệnh nhân cụ thể đã bị tổn thương. Nguồn không báo cáo rằng AI đã trực tiếp gây mất thị lực hoặc biến chứng cho bệnh nhân trong nghiên cứu.

### Harm Map Worksheet

| Trường | Phân tích của tôi |
| --- | --- |
| High-risk moment | Khi AI trả kết quả âm tính hoặc ảnh không đọc được và kết quả đó quyết định bệnh nhân có được chuyển đến bác sĩ chuyên khoa đúng lúc hay không. |
| Stakeholder bị ảnh hưởng | Người bệnh đái tháo đường; điều dưỡng/chụp ảnh viên; bác sĩ võng mạc; cơ sở chăm sóc ban đầu; Bộ Y tế Thái Lan; Google Health và các đối tác. |
| Failure mode | False negative; ảnh đầu vào không đạt chất lượng; lỗi tích hợp vào quy trình; automation bias nếu người dùng coi kết quả AI là kết luận cuối cùng. |
| Layer bắt đầu lỗi | **Grounding** khi ảnh thực tế hoặc bối cảnh triển khai không đủ chất lượng cho mô hình; **Model** đối với sai số phân loại còn lại. Safety layer phải định tuyến các ca không chắc chắn sang chuyên gia. |
| Harm xảy ra là gì? | **Đã được đo:** hệ thống không có sensitivity 100% và có nhóm bệnh nhân cần chuyển khám vì ảnh không đọc được/các dấu hiệu khác. **Chỉ là nguy cơ:** bỏ sót hoặc trì hoãn chẩn đoán dẫn đến giảm thị lực; nghiên cứu không báo cáo thiệt hại này như hậu quả do AI gây ra. |
| Harm lens | An toàn và sức khỏe; khả năng tiếp cận; độ tin cậy; human oversight; công bằng trong điều kiện cơ sở vật chất khác nhau. |
| Severity | **High.** Bỏ sót bệnh đe dọa thị lực có thể trì hoãn điều trị; nguồn chưa ghi nhận tổn thương trực tiếp do hệ thống trong nghiên cứu. |
| Scale | **Medium–High.** Nghiên cứu thực hiện trên 7.651 bệnh nhân tại 9 cơ sở và nằm trong bối cảnh chương trình sàng lọc quốc gia; không tự động suy rộng hiệu năng sang mọi quốc gia hoặc loại camera. |
| Probability | **Medium.** Sensitivity 91,4% cho thấy còn false negative trong nhóm dương tính theo chuẩn tham chiếu, nhưng xác suất tổn thương lâm sàng sau đó không được nguồn đo. |
| Frequency | Hệ thống được dùng cho từng lần sàng lọc; trong giai đoạn nghiên cứu có 7.940 người được sàng lọc. Nguy cơ xuất hiện mỗi khi ảnh kém chất lượng hoặc ca bệnh bị phân loại sai. |
| Vì sao? | Hiệu năng tổng thể cao không loại bỏ rủi ro ở từng bệnh nhân. Cơ chế chuyên gia đọc lại từng ảnh trong nghiên cứu là bằng chứng thực tế rằng human review và fail-safe routing cần được giữ khi triển khai. |

### Kiểm soát phù hợp cho case 3

- Kiểm tra chất lượng ảnh ngay tại thời điểm chụp và hướng dẫn chụp lại rõ ràng.
- Chuyển mọi ảnh không đọc được, kết quả không chắc chắn hoặc triệu chứng không phù hợp sang chuyên gia.
- Duy trì lấy mẫu đọc lại bởi bác sĩ sau triển khai, đặc biệt theo loại camera, cơ sở và nhóm bệnh nhân.
- Theo dõi tỷ lệ false negative, tỷ lệ ảnh không đọc được, thời gian đến khám chuyên khoa và bệnh nhân mất dấu sau chuyển tuyến.
- Không truyền ảnh hay dữ liệu nhận dạng vượt quá mục đích sàng lọc; áp dụng mã hóa và phân quyền truy cập.

---

## 5. So sánh ba case và bài học rút ra

| Case | Lỗi chính | Harm đã có bằng chứng | Harm chỉ là nguy cơ/suy luận | Human review cần thiết |
| --- | --- | --- | --- | --- |
| Thuật toán quản lý sức khỏe quần thể | Dùng chi phí làm proxy cho nhu cầu sức khỏe | Chênh lệch có hệ thống trong lựa chọn bệnh nhân da đen | Tổn thương lâm sàng cụ thể của từng cá nhân | Hội đồng lâm sàng và fairness review trước/sau mỗi đợt phân bổ |
| Epic Sepsis Model | Hiệu năng ngoài thấp, bỏ sót và gánh nặng cảnh báo | 67% ca sepsis không đạt ngưỡng; 18% lượt nhập viện bị gắn cờ trong phân tích | Chậm điều trị hoặc tử vong do cảnh báo ESM | Bác sĩ đánh giá độc lập; không dùng AI để loại trừ sepsis; theo dõi alert fatigue |
| AI sàng lọc võng mạc | Sai số phân loại và phụ thuộc chất lượng ảnh/quy trình | Hiệu năng và tỷ lệ chuyển khám được đo trong nghiên cứu tiến cứu | Giảm thị lực do false negative hoặc chậm chuyển tuyến | Chuyên gia đọc lại ca không chắc chắn/ảnh lỗi và kiểm tra mẫu định kỳ |

Ba case cho thấy rủi ro AI y tế không chỉ nằm trong độ chính xác của model. Harm có thể bắt đầu từ **mục tiêu sai** (dùng chi phí thay nhu cầu), từ **model không tổng quát tốt** ở cơ sở mới, hoặc từ **dữ liệu đầu vào và workflow thực tế**. Vì vậy, Responsible AI trong y tế cần kết hợp thiết kế mục tiêu đúng, kiểm định ngoài, theo dõi theo nhóm, fail-safe workflow và quyền quyết định cuối cùng của người có chuyên môn.

## 6. Tài liệu tham khảo

1. Obermeyer, Z., Powers, B., Vogeli, C., & Mullainathan, S. (2019). [Dissecting racial bias in an algorithm used to manage the health of populations](https://doi.org/10.1126/science.aax2342). *Science, 366*(6464), 447–453. DOI: 10.1126/science.aax2342.
2. Wong, A. et al. (2021). [External Validation of a Widely Implemented Proprietary Sepsis Prediction Model in Hospitalized Patients](https://doi.org/10.1001/jamainternmed.2021.2626). *JAMA Internal Medicine, 181*(8), 1065–1070. DOI: 10.1001/jamainternmed.2021.2626.
3. Ruamviboonsuk, P. et al. (2022). [Real-time diabetic retinopathy screening by deep learning in a multisite national screening programme](https://doi.org/10.1016/S2589-7500(22)00017-6). *The Lancet Digital Health, 4*(4), e235–e244. DOI: 10.1016/S2589-7500(22)00017-6. [PubMed record](https://pubmed.ncbi.nlm.nih.gov/35272972/).

## 7. Tự kiểm tra trước khi nộp

- [x] Chọn đúng một ngành: Y tế.
- [x] Có Industry Risk Snapshot với harm, high-stakes, dữ liệu nhạy cảm và human review.
- [x] Có ba case thật trong cùng ngành.
- [x] Mỗi case có mô tả, số liệu, nguồn và phân biệt bằng chứng với nhận định.
- [x] Mỗi case có Harm Map đủ 11 trường.
- [x] Không đưa dữ liệu cá nhân hoặc dữ liệu bệnh nhân thật vào repo.
- [x] Các link nguồn dùng DOI/PubMed và có thể kiểm tra độc lập.
