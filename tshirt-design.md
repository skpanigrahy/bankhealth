Worked for 5m 14s

from PIL import Image from reportlab.pdfgen import canvas from reportlab.lib.pagesizes import A4 from reportlab.lib.utils import ImageReader from reportlab.lib import colors

src="/mnt/data/a_clean_presentation_layout_with_multiple_panels_s.png" im=Image.open(src).convert("RGB")

Full mockups from the right column, preserving complete shirts.

front_mock=im.crop((1085,35,1535,495)) back_mock=im.crop((1085,495,1535,1015)) mockup=Image.new("RGB",(900,520),"white") fm=front_mock.resize((440,450),Image.Resampling.LANCZOS) bm=back_mock.resize((440,450),Image.Resampling.LANCZOS) mockup.paste(fm,(5,35)); mockup.paste(bm,(455,35)) mockup_path="/mnt/data/CBC_Technology_TShirt_Mockup_Clean.png" mockup.save(mockup_path,quality=100)

Submission PDF with exact requested structure and separate file references.

pdf_path="/mnt/data/CBC_Technology_Submission_Package_Santosh.pdf" c=canvas.Canvas(pdf_path,pagesize=A4) W,H=A4 m=40 c.setFillColor(colors.HexColor("#073B6D")) c.rect(0,H-65,W,65,fill=1,stroke=0) c.setFillColor(colors.white); c.setFont("Helvetica-Bold",18) c.drawString(m,H-42,"CBC Technology T-Shirt Design Submission")

sections=[ ("1. DESIGN FILE","CBC_Technology_Design_File_FRONT_Clean.png  |  CBC_Technology_Design_File_BACK_Clean.png"), ("2. T-SHIRT MOCKUP","CBC_Technology_TShirt_Mockup_Clean.png"), ("3. NAME + LOCATION","Santosh Kumar Panigrahy  |  Hyderabad, India"), ] y=H-105 for title,body in sections: c.setFillColor(colors.HexColor("#073B6D")); c.setFont("Helvetica-Bold",12) c.drawString(m,y,title) c.setFillColor(colors.black); c.setFont("Helvetica",10.5) c.drawString(m,y-18,body) y-=58

c.setFillColor(colors.HexColor("#073B6D")); c.setFont("Helvetica-Bold",12) c.drawString(m,y,"4. BRIEF INSPIRATION / DESCRIPTION") y-=22 desc=("“One CBC Technology. A Stronger Tomorrow Together.” This design represents CBC Technology as one global team, " "connected across geographies, cultures and disciplines. The flowing journey and diverse people symbolize " "collaboration and inclusion, while the connected world and landmarks represent our global reach. The five " "visual bands represent collaboration, innovation, security, modernization and impact, reflecting a shared " "commitment to build secure, resilient and modern core banking technology that creates meaningful value for " "customers and colleagues, today and for a stronger tomorrow.") from reportlab.platypus import Paragraph from reportlab.lib.styles import ParagraphStyle style=ParagraphStyle("d",fontName="Helvetica",fontSize=10.5,leading=15,textColor=colors.black) p=Paragraph(desc,style) p.wrapOn(c,W-2*m,170); p.drawOn(c,m,y-155) c.save()

print(mockup_path) print(pdf_path)

Done. I’ve cleaned up the package so the mockup is separate from the artwork, and the full T-shirts are visible in the mockup.

1. Design File

These are the artwork files for the front and back:

Front Design File

Back Design File

Combined Front + Back Design


2. T-Shirt Mockup

This is a separate file showing the complete front and back T-shirts:

Download Full T-Shirt Mockup

3. Name + Location

Santosh Kumar Panigrahy
Hyderabad, India

4. Brief Inspiration / Description

“One CBC Technology. A Stronger Tomorrow Together.”

> This design represents CBC Technology as one global team, connected across geographies, cultures and disciplines. The flowing journey and diverse people symbolize collaboration and inclusion, while the connected world and landmarks represent our global reach. The five visual bands represent collaboration, innovation, security, modernization and impact, reflecting a shared commitment to build secure, resilient and modern core banking technology that creates meaningful value for customers and colleagues, today and for a stronger tomorrow.



Complete package

Download Submission Package PDF

One caveat: the artwork files I'm giving you here are high-resolution PNG artwork exports, not editable vector source files. Since the contest instructions you showed do not specify a required file format, PNG/PDF is reasonable for submission. If Carson's linked guidelines specifically require AI/SVG/EPS/PSD, then we should follow that requirement instead. ❶


Worked for 5m 14s

from PIL import Image from reportlab.pdfgen import canvas from reportlab.lib.pagesizes import A4 from reportlab.lib.utils import ImageReader from reportlab.lib import colors

src="/mnt/data/a_clean_presentation_layout_with_multiple_panels_s.png" im=Image.open(src).convert("RGB")

Full mockups from the right column, preserving complete shirts.

front_mock=im.crop((1085,35,1535,495)) back_mock=im.crop((1085,495,1535,1015)) mockup=Image.new("RGB",(900,520),"white") fm=front_mock.resize((440,450),Image.Resampling.LANCZOS) bm=back_mock.resize((440,450),Image.Resampling.LANCZOS) mockup.paste(fm,(5,35)); mockup.paste(bm,(455,35)) mockup_path="/mnt/data/CBC_Technology_TShirt_Mockup_Clean.png" mockup.save(mockup_path,quality=100)

Submission PDF with exact requested structure and separate file references.

pdf_path="/mnt/data/CBC_Technology_Submission_Package_Santosh.pdf" c=canvas.Canvas(pdf_path,pagesize=A4) W,H=A4 m=40 c.setFillColor(colors.HexColor("#073B6D")) c.rect(0,H-65,W,65,fill=1,stroke=0) c.setFillColor(colors.white); c.setFont("Helvetica-Bold",18) c.drawString(m,H-42,"CBC Technology T-Shirt Design Submission")

sections=[ ("1. DESIGN FILE","CBC_Technology_Design_File_FRONT_Clean.png  |  CBC_Technology_Design_File_BACK_Clean.png"), ("2. T-SHIRT MOCKUP","CBC_Technology_TShirt_Mockup_Clean.png"), ("3. NAME + LOCATION","Santosh Kumar Panigrahy  |  Hyderabad, India"), ] y=H-105 for title,body in sections: c.setFillColor(colors.HexColor("#073B6D")); c.setFont("Helvetica-Bold",12) c.drawString(m,y,title) c.setFillColor(colors.black); c.setFont("Helvetica",10.5) c.drawString(m,y-18,body) y-=58

c.setFillColor(colors.HexColor("#073B6D")); c.setFont("Helvetica-Bold",12) c.drawString(m,y,"4. BRIEF INSPIRATION / DESCRIPTION") y-=22 desc=("“One CBC Technology. A Stronger Tomorrow Together.” This design represents CBC Technology as one global team, " "connected across geographies, cultures and disciplines. The flowing journey and diverse people symbolize " "collaboration and inclusion, while the connected world and landmarks represent our global reach. The five " "visual bands represent collaboration, innovation, security, modernization and impact, reflecting a shared " "commitment to build secure, resilient and modern core banking technology that creates meaningful value for " "customers and colleagues, today and for a stronger tomorrow.") from reportlab.platypus import Paragraph from reportlab.lib.styles import ParagraphStyle style=ParagraphStyle("d",fontName="Helvetica",fontSize=10.5,leading=15,textColor=colors.black) p=Paragraph(desc,style) p.wrapOn(c,W-2*m,170); p.drawOn(c,m,y-155) c.save()

print(mockup_path) print(pdf_path)

Done. I’ve cleaned up the package so the mockup is separate from the artwork, and the full T-shirts are visible in the mockup.

1. Design File

These are the artwork files for the front and back:

Front Design File

Back Design File

Combined Front + Back Design


2. T-Shirt Mockup

This is a separate file showing the complete front and back T-shirts:

Download Full T-Shirt Mockup

3. Name + Location

Santosh Kumar Panigrahy
Hyderabad, India

4. Brief Inspiration / Description

“One CBC Technology. A Stronger Tomorrow Together.”

> This design represents CBC Technology as one global team, connected across geographies, cultures and disciplines. The flowing journey and diverse people symbolize collaboration and inclusion, while the connected world and landmarks represent our global reach. The five visual bands represent collaboration, innovation, security, modernization and impact, reflecting a shared commitment to build secure, resilient and modern core banking technology that creates meaningful value for customers and colleagues, today and for a stronger tomorrow.



Complete package

Download Submission Package PDF

One caveat: the artwork files I'm giving you here are high-resolution PNG artwork exports, not editable vector source files. Since the contest instructions you showed do not specify a required file format, PNG/PDF is reasonable for submission. If Carson's linked guidelines specifically require AI/SVG/EPS/PSD, then we should follow that requirement instead. ❶


Done. I’ve now separated the actual artwork from the T-shirt mockup, which is the correct way to package this contest entry.

1. Design File

Front artwork only, no T-shirt/model:
Download Front Design File

Back artwork only, no T-shirt/model:
Download Back Design File

Combined Front + Back artwork:
Download Combined Design File

2. T-Shirt Mockup

This is separate from the artwork and shows the white cotton polo with navy collar/sleeves, front and back.

Download T-Shirt Mockup

3. Name + Location

Santosh Kumar Panigrahy
Hyderabad, India

4. Brief Inspiration / Description

“One CBC Technology. A Stronger Tomorrow Together.”

> This design represents CBC Technology as one global team, connected across geographies, cultures and disciplines. The people moving together and the connected world symbolize collaboration, inclusion and our global reach. The flowing journey and landmarks represent our diverse teams and shared purpose. The five themes, Collaborate, Innovate, Secure, Modernize and Impact, reflect our commitment to building resilient, secure and modern core banking technology that creates meaningful value for customers and colleagues, today and for a stronger tomorrow.



Complete package

Download Complete Submission Package PDF

For Carson, I would send these three primary attachments:

1. Front Design File


2. Back Design File


3. T-Shirt Mockup



And include your name, location and inspiration description in the email body or the accompanying PDF.

The SVG/AI question: since the guideline you showed does not specify a required file format, I would not fabricate an SVG from the raster artwork. The PNG files are suitable visual design exports. If Carson's linked guideline later specifies AI/SVG/EPS/PDF, we should follow that exact requirement. ❶


I've revised the concept based directly on Carson's feedback and the examples he sent.

1. Design File. Front

Clean artwork, without a T-shirt or model.

Download Front Design

2. Design File. Back

Clean artwork, without a T-shirt or model.

Download Back Design

3. Combined Design File

Front + back artwork together.

Download Combined Front + Back Design

4. T-Shirt Mockup

Now changed from the polo to a regular crew-neck T-shirt, with the design shown on both front and back.

Download T-Shirt Mockup

5. Color Options

I also prepared the limited-color variations Carson suggested.

Download Color Options

The primary version is:

Navy T-shirt + White + CBC Blue

with charcoal and black alternatives.

6. Name + Location

Santosh Kumar Panigrahy
Hyderabad, India

7. Inspiration

“One CBC Technology. A Stronger Tomorrow Together.”

> This design represents CBC Technology as one global team, connected across geographies, cultures and disciplines. The connected world and network lines symbolize collaboration across people, platforms and locations. The simple technology-forward visual language reflects innovation, security and modernization, while the people, communities and impact elements connect our technology work to customers and colleagues. The limited color palette is intentionally clean and wearable, making the design practical for an everyday T-shirt while preserving a strong global CBC Technology identity.



Complete submission

Download Complete Revised Submission PDF

I think this revised direction is much closer to what Carson is asking for. The important change is that we have kept your original global team / One CBC Technology idea, but translated it into a simpler, more wearable T-shirt design rather than a detailed polo graphic. ❶
