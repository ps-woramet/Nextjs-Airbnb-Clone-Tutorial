trick (my snippets)

    ไปที่ "File" > "Preferences" > "User Snippets".
    เลือก "html.json", "tsx.json" หรือ "jsx.json" แล้วสร้างหรือแก้ไข snippet ตามต้องการ:

    {
        "tailwindDiv": {
            "prefix": "qf",
            "body": [
            "<div class=\"flex flex-row items-center justify-between\"></div>"
            ],
            "description": "Create a Tailwind CSS div with specific classes"
        }
    }

trick (usememo, usecallback)

    เวลาสร้าง component หรือ re render component จะทำการกำหนดค่า ตัวแปร, function ใหม่ตลอด ยกเว้น useState, props จะไม่สร้างใหม่จะถูก update ค่า
    
    ตัวอย่าง

        -เมื่อสร้าง component ครั้งแรกจะสร้าง state count,  ตัวแปร value1 = "1", ฟังก์ชั่น function func1{console.log('hello');}
        -เมื่อค่า count เปลี่ยนทำให้เกิดการ re component จึ้งต้อง update state count, สร้างตัวแปร value1 = "1", สร้าง function ฟังก์ชั่น function func1{console.log('hello');}
        จะเห็นได้ว่า ค่าตัวแปร กับ ค่า function ยังเป็นตัวเดิมแต่มีการสร้างขึ้นมาใหม่

        const ComponentA = () => {
     
            const [count, setCount] = useState(0)
            const value1 = "1";
            function func1{
                console.log('hello');
            }

            return (<div><button onClick={() => setCount(count+1)}></div>)
        }

        useMemo จะช่วยให้เวลา re render component ไม่ต้องกำหนดค่า ตัวแปร ใหม่
        useCallback จะช่วยให้เวลา re render component ไม่ต้องกำหนดค่า function ใหม่

        วิธีใช้
            useCallback(()=>{console.log('hello')}, []) //function ถูกสร้างครั้งแรกครั้งเดียว
            useCallback(()=>{console.log('hello')}, [value]) //ถ้าค่าใน dependencies เปลี่ยนจะสร้าง functionใหม่,ถ้าไม่เปลี่ยนจะไม่สร้าง

0. install project

    > npx create-next-app --typescript
    √ What is your project named? ... nextjs-airbnb-clone-tutorial
    √ Would you like to use ESLint? Yes
    √ Would you like to use Tailwind CSS? Yes
    √ Would you like to use `src/` directory? No
    √ Would you like to use App Router? (recommended) Yes
    √ Would you like to customize the default import alias (@/*)? Yes
    √ What import alias would you like configured? ... @/* Enter

    nextjs-airbnb-clone-tutorial > npm install react-icons
    nextjs-airbnb-clone-tutorial > npm run dev

1. ทดสอบค่า localhost

    -app > page.tsx

        export default function Home() {
            return (
                <div className="text-red-500">hello woramet</div>
            )
        }

    -app > globals.CSS

        @tailwind base;
        @tailwind components;
        @tailwind utilities;

2. สร้าง navbar component //ค่า typescript ส่วนใหญ่จะถูกใช้เมื่อส่งค่า props ผ่าน component

    -public > images > avatarImages.svg, logo.svg

    -app > components > navbar > logo.tsx, navbar.tsx, menuitem.tsx, Search.tsx, UserMenu.tsx

    -app > components > Container.tsx //เมื่อ component container คลอบ component อื่น ค่า component อื่นจะถูกส่งเป็น children

        'use client'
        import React from 'react'

        interface ContainerProps {
            children: React.ReactNode;
        }

        const Container:React.FC<ContainerProps> = ({children}) => {
            return (
                <div className='max-w-[2520px] mx-auto xl:px-2 md:px-10 sm:px-2 px-4'>
                    {children}
                </div>
            )
        }

        export default Container

     -app > components > Avatar.tsx

        'use client'

        import Image from 'next/image'
        import React from 'react'

        const Avatar = () => {
            return (
                <Image className='rounded-full'
                    height="30"
                    width="30"
                    alt='Avatar'
                    src="/images/avatarImage.svg"
                />
            )
        }

        export default Avatar

    -app > layout.tsx

        import './globals.css'
        import type { Metadata } from 'next'
        import { Nunito } from 'next/font/google'
        import Navbar from './components/navbar/Navbar'

        export const metadata: Metadata = {
            title: 'Airbnb',
            description: 'Airbnb clone',
        }

        const font = Nunito({ subsets: ['latin'] })

        export default function RootLayout({
                children,
            }: {
                children: React.ReactNode
            }) {
            return (
                <html lang="en">

                <body className={font.className}>
                    <Navbar/>
                    {children}
                </body>
                </html>
            )
        }

3.
    