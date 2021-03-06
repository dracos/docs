---
$title: AMP ช่วยเพิ่มประสิทธิภาพได้อย่างไร
---
[TOC]

การเพิ่มประสิทธิภาพต่อไปนี้รวมกันคือเหตุผลที่ทำให้หน้า AMP สามารถทำงานได้อย่างรวดเร็วเหมือนกับโหลดข้อมูลได้ในทันที

หากคุณชอบการฟังมากกว่าการอ่าน คุณสามารถดูวิดีโอต่อไปนี้จาก Malte Ubl หัวหน้าทีมวิศวกรที่พัฒนา AMP ซึ่งจะให้ภาพรวมของข้อมูลเช่นเดียวกับข้อความด้านล่าง

<amp-youtube
    data-videoid="hVRkG1CQScA"
    layout="responsive"
    width="480" height="270">
</amp-youtube>

## อนุญาตเฉพาะสคริปต์แบบอะซิงโครนัส

JavaScript เป็นการทำงานที่มีประสิทธิภาพสูง
โดยสามารถปรับเปลี่ยนหน้าเว็บในทุกๆ ด้าน
อย่างไรก็ตาม การทำงานดังกล่าวอาจบล็อกการสร้าง DOM ทำให้แสดงหน้าเว็บได้ช้าลง
(ดูเพิ่มเติมเกี่ยวกับ[การเพิ่มคุณสมบัติแบบอินเทอร์แอคทีฟด้วย JavaScript](https://developers.google.com/web/fundamentals/performance/critical-rendering-path/adding-interactivity-with-javascript))
เพื่อป้องกันไม่ให้ JavaScript แสดงหน้าเว็บช้าลง
AMP จึงอนุญาตเฉพาะ JavaScript แบบอะซิงโครนัส

หน้า AMP ไม่อนุญาตให้มี JavaScript ที่นักพัฒนาเป็นผู้เขียนขึ้น
คุณสมบัติหน้าเว็บแบบอินเทอร์แอคทีฟจึงถูกจัดการโดยอิลิเมนต์ AMP แบบกำหนดเองแทนการใช้ JavaScript

อย่างไรก็ตาม อิลิเมนต์ AMP อาจมี JavaScript
ทำงานอยู่เบี้องหลังได้โดยมีการออกแบบเป็นอย่างดีเพื่อให้แน่ใจว่าจะไม่ไปลดประสิทธิภาพ

แม้ว่าจะสามารถใช้ JS ของบุคคลที่สามใน iframe ได้
แต่จะต้องไม่บล็อกการแสดงผล
ตัวอย่างเช่น หาก JS ของบุคคลที่สามใช้
[super-bad-for-performance `document.write` API](http://www.stevesouders.com/blog/2012/04/10/dont-docwrite-scripts/)
การใช้งานดังกล่าวจะต้องไม่บล็อกการแสดงหน้าหลัก

## กำหนดขนาดทรัพยากรทั้งหมดแบบคงที่

ทรัพยากรภายนอก เช่น รูปภาพ โฆษณา หรือ iframe ต้องกำหนดขนาดไว้ใน HTML
เพื่อให้ AMP สามารถพิจารณาขนาดและตำแหน่งของแต่ละอิลิเมนต์ก่อนที่จะดาวน์โหลดทรัพยากร
AMP จะโหลดเค้าโครงของหน้าโดยไม่รอการดาวน์โหลดทรัพยากร

AMP จะแยกเค้าโครงเอกสารออกจากเค้าโครงของทรัพยากร
ในการจัดเค้าโครงเอกสารทั้งหมดจะใช้คำขอ HTTP เพียงรายการเดียว
([+แบบอักษร](#font-triggering-must-be-efficient))
เนื่องจาก AMP เพิ่มประสิทธิภาพโดยหลีกเลี่ยงการคำนวณรูปแบบและเค้าโครงในเบราว์เซอร์ใหม่
จึงไม่จำเป็นต้องมีการเปลี่ยนเค้าโครงเมื่อโหลดทรัพยากร

## ป้องกันไม่ให้กลไกส่วนขยายบล็อกการแสดงผล

AMP จะป้องกันไม่ให้กลไกส่วนขยายบล็อกการแสดงผล
AMP สนับสนุนส่วนขยายต่างๆ เช่น
[lightboxes](/docs/reference/extended/amp-lightbox.html),
[instagram embeds](/docs/reference/extended/amp-instagram.html),
[tweets](/docs/reference/extended/amp-twitter.html) ฯลฯ
แม้ว่าส่วนขยายเหล่านี้จะต้องมีคำขอ HTTP เพิ่มเติม
คำขอเหล่านั้นไม่ได้บล็อกเค้าโครงหน้าเว็บและการแสดงผล

หน้าเว็บใดที่ใช้สคริปต์แบบกำหนดเองจะต้องแจ้งให้ระบบ AMP ทราบว่าจะต้องมีแท็กแบบกำหนดเอง

ตัวอย่างเช่น สคริปต์ [`amp-iframe`](/docs/reference/extended/amp-iframe.html)
จะบอกให้ระบบทราบว่าจะมีแท็ก `amp-iframe`
AMP จะสร้างกรอบ iframe ก่อนที่จะทราบว่ามีอะไรอยู่ภายใน

[sourcecode:html]
<script async custom-element="amp-iframe" src="https://cdn.ampproject.org/v0/amp-youtube-0.1.js"></script>
[/sourcecode]

## เอา JavaScript ของบุคคลที่สามทั้งหมดออกจากพาธที่สำคัญ

JS ของบุคคลที่สามมักใช้การโหลด JS แบบซิงโครนัส
นอกจากนี้ยังมีการใช้ `document.write` สำหรับการซิงค์ข้อมูลด้วย
ตัวอย่างเช่น หากคุณมีโฆษณาห้ารายการ โดยแต่ละรายการมีการซิงค์ข้อมูล
โดยใช้เวลาแฝง 1 วินาที
คุณจะต้องใช้เวลาถึง 15 วินาทีสำหรับการโหลด JS

หน้า AMP จะอนุญาต JavaScript ของบุคคลที่สามได้แต่เฉพาะใน iframe ที่มีแซนด์บ็อกซ์เท่านั้น
การจำกัดการใช้งานเฉพาะใน iframe จะทำให้ไม่สามารถบล็อกการทำงานของหน้าหลักได้
หรือแม้แต่การเริ่มการคำนวณรูปแบบหลายครั้ง
iframe ที่มีขนาดเล็กจะมี DOM ที่เล็กมาก

การคำนวณรูปแบบและเค้าโครงใหม่จะขึ้นอยู่กับขนาด DOM
ดังนั้น การคำนวณใหม่ของ iframe จะมีการทำงานที่รวดเร็วมากเมื่อเทียบกับการคำนวณรูปแบบและเค้าโครงของหน้าเว็บใหม่


## CSS ทั้งหมดจะต้องแสดงแบบอินไลน์และในขนาดที่กำหนดไว้

CSS จะบล็อกการแสดงผลทั้งหมด รวมทั้งบล็อกการโหลดหน้าเว็บ และอาจมีการขยายการทำงานได้อย่างไม่สิ้นสุด
ในหน้า AMP HTML จะสามารถแสดงได้เฉพาะในรูปแบบอินไลน์
ซึ่งเป็นการเอาคำขอ HTTP อย่างน้อย 1 รายการออกจากพาธการแสดงผลที่สำคัญเมื่อเปรียบเทียบกับหน้าเว็บส่วนใหญ่


นอกจากนี้รูปแบบอินไลน์ยังมีขนาดไม่เกิน 50 กิโลไบต์
แม้ว่าขนาดดังกล่าวจะใหญ่พอสำหรับหน้าเว็บที่มีความซับซ้อนมาก
ผู้เขียนหน้าเว็บยังคงต้องมีการปรับแต่ง CSS เพื่อการทำงานได้อย่างมีประสิทธิภาพ

## การเรียกใช้แบบอักษรจะต้องมีประสิทธิภาพ

แบบอักษรที่ใช้บนเว็บมีขนาดใหญ่มาก ดังนั้น[การเพิ่มประสิทธิภาพแบบอักษรบนเว็บ](https://developers.google.com/web/fundamentals/performance/optimizing-content-efficiency/webfont-optimization)จึงเป็นเรื่องสำคัญอย่างยิ่ง


บนหน้าเว็บทั่วไปที่มีสคริปต์การซิงค์ข้อมูลและสไตล์ชีทภายนอกไม่มากนัก
เบราว์เซอร์จะยังไม่เริ่มการดาวน์โหลดแบบอักษรที่มีขนาดใหญ่เหล่านี้จนกว่าการทำงานทั้งหมดจะเสร็จสมบูรณ์

ระบบ AMP จะประกาศคำขอ HTTP เป็นศูนย์จนกว่าจะมีการเริ่มการดาวน์โหลดแบบอักษร
ซึ่งสามารถทำได้เนื่องจาก JS ทั้งหมดใน AMP มีแอททริบิวต์ async
และอนุญาตเฉพาะสไตล์ชีทแบบอินไลน์เท่านั้น
จึงไม่มีคำขอ HTTP ที่จะบล็อกเบราว์เซอร์ไม่ให้ดาวน์โหลดแบบอักษรได้

## ลดการคำนวณรูปแบบใหม่

ระบบจะเริ่มการคำนวณรูปแบบใหม่ทุกครั้งที่คุณตรวจสอบบางสิ่ง ซึ่งทำให้สิ้นเปลืองทรัพยากรอย่างมาก
เนื่องจากเบราว์เซอร์จะต้องวางเค้าโครงทั้งหน้า
ในหน้า AMP การอ่านข้อมูลทั้งหมดของ DOM จะเกิดขึ้นก่อนการเขียน
เพื่อให้แน่ใจว่าจะมีการคำนวณรูปแบบใหม่สำหรับแต่ละเฟรมไม่เกินหนึ่งครั้ง

เรียนรู้เพิ่มเติมเกี่ยวกับผลกระทบของการคำนวณรูปแบบและเค้าโครงใหม่ได้ใน[ประสิทธิภาพในการแสดงผล](https://developers.google.com/web/fundamentals/performance/rendering/)


## เรียกใช้เฉพาะภาพเคลื่อนไหวที่รองรับการเร่งการแสดงผลกราฟิก

วิธีการเดียวที่จะช่วยให้สามารถแสดงผลได้อย่างรวดเร็วคือการเรียกใช้บน GPU
ซึ่ง GPU จะรู้จักเลเยอร์ต่างๆ รวมทั้งวิธีดำเนินการบนเลเยอร์เหล่านี้
โดยสามารถย้ายหรือทำให้เลือนหาย แต่ไม่สามารถอัปเดตเค้าโครงหน้าได้
และจะส่งงานนั้นผ่านไปยังเบราว์เซอร์ ซึ่งไม่ใช่วิธีการที่ดี

กฎสำหรับ CSS ที่เกี่ยวข้องกับภาพเคลื่อนไหวจะช่วยให้แน่ใจได้ว่าภาพเคลื่อนไหวดังกล่าวจะรองรับการเร่งการแสดงผลกราฟิก
โดยเฉพาะอย่างยิ่ง AMP จะอนุญาตเฉพาะการแสดงภาพเคลื่อนไหวและการเปลี่ยนรูปร่างและความโปร่งใส
ซึ่งไม่จำเป็นต้องใช้เค้าโครงหน้า
เรียนรู้เพิ่มเติมเกี่ยวกับ[การใช้การเปลี่ยนรูปร่างและความโปร่งใสสำหรับการเปลี่ยนภาพเคลื่อนไหว](https://developers.google.com/web/fundamentals/performance/rendering/stick-to-compositor-only-properties-and-manage-layer-count)


## จัดลำดับการโหลดทรัพยากร

AMP จะควบคุมการดาวน์โหลดทรัพยากรทั้งหมด จัดลำดับการโหลด
โหลดเฉพาะสิ่งที่จำเป็น และดึงทรัพยากรมาเท่าที่จำเป็นก่อน

เมื่อ AMP ดาวน์โหลดทรัพยากร จะมีการเพิ่มประสิทธิภาพการดาวน์โหลด
เพื่อให้มีการดาวน์โหลดทรัพยากรที่สำคัญมากที่สุดในขณะนั้นมาก่อน
รูปภาพและโฆษณาจะได้รับการดาวน์โหลดเฉพาะเมื่อจะปรากฏต่อสายตาของผู้ใช้
แสดงบนหน้าโดยไม่ต้องเลื่อนดู หรือเมื่อผู้ใช้ต้องการเลื่อนดูรายการเหล่านั้นอย่างรวดเร็ว

นอกจากนี้ AMP ยังมีการดึงทรัพยากรมาเท่าที่จำเป็นก่อน
การโหลดทรัพยากรจะเริ่มช้าที่สุดเท่าที่จะทำได้ แต่จะพยายามดึงทรัพยากรเท่าที่จำเป็นมาให้เร็วที่สุด
วิธีการดังกล่าวจะช่วยให้สามารถโหลดได้อย่างรวดเร็ว โดยมีการใช้ CPU เฉพาะเมื่อจำเป็นต้องทำให้ทรัพยากรเหล่านั้นปรากฏต่อสายตาของผู้ใช้


## โหลดหน้าเว็บได้ในทันที

[API ที่เชื่อมต่อล่วงหน้า](http://www.w3.org/TR/resource-hints/#dfn-preconnect)ใหม่จะถูกนำมาใช้อย่างมากเพื่อให้แน่ใจว่าจะสามารถส่งคำขอ
HTTP ได้อย่างรวดเร็วเมื่อต้องการ
การทำงานนี้จะช่วยให้สามารถแสดงหน้าเว็บได้ก่อนที่ผู้ใช้จะแจ้งว่าต้องการไปที่รายการนั้น
หน้าเว็บดังกล่าวอาจพร้อมใช้งานอยู่แล้วในเวลาที่ผู้ใช้เลือก
ทำให้สามารถโหลดได้ในทันที


แม้ว่าการแสดงผลล่วงหน้าจะสามารถนำไปใช้กับเนื้อหาเว็บทั้งหมด
แต่การใช้งานนี้จะใช้แบนด์วิดธ์และ CPU มาก AMP จะเพิ่มประสิทธิภาพเพื่อลดผลกระทบเหล่านี้ การแสดงผลล่วงหน้าจะดาวน์โหลดเฉพาะรายการที่จะปรากฏบนหน้าโดยไม่ต้องเลื่อนดู
โดยจะไม่แสดงผลรายการที่อาจใช้ทรัพยากรมากในแง่ของ CPU

เมื่อเอกสาร AMP แสดงผลล่วงหน้าเพื่อให้สามารถโหลดได้ในทันที
จะมีการดาวน์โหลดเฉพาะรายการที่แสดงขึ้นโดยไม่ต้องเลื่อนดูเท่านั้น
เมื่อเอกสาร AMP แสดงผลล่วงหน้าเพื่อให้สามารถโหลดได้ในทันที
จะไม่มีการดาวน์โหลดทรัพยากรที่อาจใช้ CPU มาก (เช่น iframe ของบุคคลที่สาม)

เรียนรู้เพิ่มเติมเกี่ยวกับ[เหตุใด AMP HTML จึงไม่ใช้ประโยชน์เต็มที่จากการสแกนเพื่อโหลดข้อมูลล่วงหน้า](https://medium.com/@cramforce/why-amp-html-does-not-take-full-advantage-of-the-preload-scanner-7e7f788aa94e)


## ช่วยให้ AMP ทำงานได้รวดเร็วขึ้น
AMP คือการทำงานร่วมกันในรูปแบบโอเพ่นซอร์ส
เราต้องการความช่วยเหลือจากคุณเพื่อทำให้ AMP ทำงานได้เร็วยิ่งขึ้น
เรียนรู้เกี่ยวกับ[วิธีการมีส่วนร่วม](/docs/support/contribute.html)
