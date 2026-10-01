from PIL import Image
from reportlab.pdfgen import canvas
from reportlab.lib.utils import ImageReader
from reportlab.lib.pagesizes import landscape, A4
import os

src = "/mnt/data/a_clean_graphic_presentation_poster_layout_with.png"
im = Image.open(src).convert("RGB")

# 1) T-shirt mockup: front + back views
mockup = im.crop((0, 0, 1085, 650))
mockup_path = "/mnt/data/CBC_Technology_TShirt_Mockup_Santosh.png"
mockup.save(mockup_path, quality=95)

# 2) Design artwork reference: front/back close-ups
design = im.crop((1080, 0, 1536, 810))
design_path = "/mnt/data/CBC_Technology_Design_File_Santosh.png"
design.save(design_path, quality=95)

# 3) Submission PDF containing the complete presentation board
pdf_path = "/mnt/data/CBC_Technology_TShirt_Submission_Santosh.pdf"
page_w, page_h = landscape(A4)
c = canvas.Canvas(pdf_path, pagesize=(page_w, page_h))
margin = 18
scale = min((page_w - 2*margin) / im.width, (page_h - 2*margin) / im.height)
w, h = im.width * scale, im.height * scale
c.drawImage(ImageReader(im), (page_w-w)/2, (page_h-h)/2, width=w, height=h, preserveAspectRatio=True, mask='auto')
c.showPage()
c.save()

print("Created:")
print(mockup_path)
print(design_path)
print(pdf_path)


Worked for 1m 7s

Exactly. The contest is asking for four things, and we should prepare the submission accordingly:

1. Design file


2. T-shirt mockup


3. Name + location


4. Brief inspiration/description



I’ve prepared the submission package around the “One CBC Technology. A Stronger Tomorrow Together.” concept.

1. Design file

Download the CBC Technology Design File

2. T-shirt mockup

Download the T-shirt Mockup

3. Submission details

Name: Santosh Kumar Panigrahy
Location: Hyderabad, India

4. Inspiration behind the design

> “One CBC Technology. A Stronger Tomorrow Together.”

This design represents CBC Technology as one global team, connected across geographies, cultures and disciplines. The globe and landmarks showcase our global presence and diversity, while the people silhouettes symbolize collaboration and inclusion.

The connected network reflects how technology enables a resilient, secure and modern core banking ecosystem. The design highlights our shared commitment to innovate, modernize and create meaningful impact for our customers and colleagues, today and for a stronger tomorrow.



Complete submission PDF

I also put everything together into one presentation-ready file:

Download Complete CBC Technology T-Shirt Submission

Email submission can be very simple:

> Subject: CBC Technology T-Shirt Design Contest Submission – Santosh Kumar Panigrahy

Hi Carson,

Please find attached my submission for the CBC Technology T-Shirt Design Contest.

Name: Santosh Kumar Panigrahy
Location: Hyderabad, India
Design: One CBC Technology. A Stronger Tomorrow Together.

Thank you for the opportunity to participate.

Regards,
Santosh



One important point: the contest says all artwork must be original and you must have the rights to the artwork used. So before sending it, I would keep the design based on original generated artwork and avoid incorporating any third-party copyrighted logos, photographs, or stock illustrations. ❶




Worked for 1m 21s

Absolutely. I’ve structured it exactly around the four items the contest asks for:

1. Design File

Download Design File - PNG

2. T-Shirt Mockup

Download T-Shirt Mockup - Front & Back

3. Name + Location

Name: Santosh Kumar Panigrahy
Location: Hyderabad, India

4. Brief Inspiration / Description

One CBC Technology. A Stronger Tomorrow Together.

> The design captures our journey as one global CBC Technology team, walking together towards a brighter tomorrow. The flowing path represents collective progress, from collaboration and new ideas to secure and modern platforms that create real impact for our customers and colleagues.

The global landmarks and connected world represent our diverse teams and worldwide reach. The rising path symbolizes a stronger, more innovative future we are building together. The design reflects our people, our purpose, and our commitment to modernize core banking technology and make a meaningful difference, today and for generations to come.



Complete submission document

Download Complete Submission PDF

For the email, I would attach the PNG design file + PNG mockup, and use the name/location/description in the email or PDF as required by Carson. ❶
