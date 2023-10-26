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

3. npm install zustand // จัดการ state
    npm install axios
    npm install react-hook-form
    npm install react-hot-toast // แสดง error

    -components > clientOnly.tsx //สร้างเพื่อแสดง {children} หลังจาก useEffect

        'use client'
        import React, { useEffect, useState } from 'react'

        interface ClientOnlyProps{
            children: React.ReactNode;
        }

        const ClientOnly:React.FC<ClientOnlyProps> = ({children}) => {
        const [hasMounted, setHasMounted] = useState(false);
        
        useEffect(()=>{setHasMounted(true)},[]);

        if (!hasMounted){
            return null;
        }

        return (
            <>
                {children}
            </>
        )
        }

        export default ClientOnly

    -components > providers > ToasterProvider.tsx //ใช้สำหรับแสดง error

        'use client';

        import { Toaster } from 'react-hot-toast';
        import React from 'react'

        const ToasterProvider = () => {
        return (
            <div>
            <Toaster/>
            </div>
        )
        }

        export default ToasterProvider

    -components > hook > useRegisterModal.tsx //จัดการ state ด้วย zustand

        import {create} from 'zustand';

        interface RegisterModalstore {
            isOpen: boolean;
            onOpen: () => void;
            onClose: () => void;
        }

        const useRegisterModal = create<RegisterModalstore>((set) => ({
            isOpen: false,
            onOpen: () => set({isOpen: true}),
            onClose: () => set({isOpen: false}),
        }));

        export default useRegisterModal;

    -components > navbar > UserMenu.tsx // เรียกใช้ useRegisterModal.tsx

        'use client'

        import React, { useState, useCallback } from 'react'
        import {AiOutlineMenu} from 'react-icons/ai';
        import Avatar from '../Avatar';
        import MenuItem from './MenuItem';
        import useRegisterModal from '@/app/hooks/useRegisterModal';


        const UserMenu = () => {

        const registerModal = useRegisterModal();
        
        const [isOpen, setIsOpen] = useState(false)
        const toggleOpen = useCallback(() => {
            setIsOpen((value) => !value);
        }, []);

        return (
            <div className='relative'>
            <div className="flex flex-row items-center gap-3">
                <div onClick={toggleOpen} className='hidden md:block text-sm font-semibold py-3 px-4 rounded-full hover:bg-neutral-100 transition cursor-pointer'>
                    airbnb your home
                </div>
                <div onClick={()=>{}} className="p-4 md:py-1 md:px-2 border-[1px] flex flex-row items-center gap-3 rounded-full cursor-pointer hover:shadow-md transition">
                    <AiOutlineMenu/>
                    <div className='hidden md:block'>
                        <Avatar/>
                    </div>
                </div>
                {isOpen
                && 
                (<div className='absolute rounded-xl shadow-md w-[40vw] md:w-3/4 bg-white overflow-hidden right-0 top-12 text-sm'>
                <div className='flex flex-col cursor-pointer'>
                    <>
                    <MenuItem onClick={() => {}} label="Login"/>
                    <MenuItem onClick={registerModal.onOpen} label="Sign up"/>
                    </>
                </div>
                </div>)}
            </div>
            </div>
        )
        }

        export default UserMenu

    -components > Heading.tsx

        'use client';
        import React from 'react'

        interface HeadingProps {
            title: string;
            subtitle?: string;
            center?: boolean;
        }

        const Heading:React.FC<HeadingProps> = ({
            title,
            subtitle,
            center
        }) => {
        return (
            <div className={center ? 'text-center': 'text-start'}>
                <div className='text-2xl font-bold'>
                    {title}
                </div>
                <div className='font-light text-neutral-500 mt-2'>
                    {subtitle}
                </div>
            </div>
        )
        }

        export default Heading

    -components > inputs > input.tsx // คอยรับค่า props ตาม useForm hook

        'use client';
        import React from 'react'
        import {FieldErrors, FieldValues, UseFormRegister} from 'react-hook-form'
        import { BiDollar } from 'react-icons/bi';

        interface InputProps {
            id: string;
            label: string;
            type?: string;
            disabled?: boolean;
            formatPrice?: boolean;
            required?: boolean;
            register: UseFormRegister<FieldValues>,
            errors: FieldErrors
        }

        const Input:React.FC<InputProps> = ({
            id,
            label,
            type = 'text',
            disabled,
            formatPrice,
            required,
            register,
            errors,
        }) => {
        return (
            <div className='w-full relative'>
            {formatPrice && (<BiDollar size={24} className="text-neutral-700 absolute top-5 left-2"/>)}
                <input id={id} disabled={disabled} {...register(id, {required})} placeholder='' type={type}
                className={`
                peer
                w-full
                p-4
                pt-6
                font-light
                bg-white
                border-2
                rounded-md
                outline-none
                transition
                disabled:opacity-70
                disabled:cursor-not-allowed
                ${formatPrice ? 'pt-9' : 'pl-4'}
                ${errors[id]  ? 'border-rose-500' : 'border-neutral-300'}
                ${errors[id] ? 'focus:border-rose-500' : 'focus:border-black'}
                `}/>

                <label 
                className={`
                absolute
                text-md
                duration-150
                transform
                -translate-y-3
                top-5
                z-10
                origin-[0]
                ${formatPrice ? 'left-9' : 'left-4'}
                peer-placeholder-shown:scale-100
                peer-placeholder-shown:translate-y-0
                peer-focus:scale-75
                peer-focus:-translate-y-4
                ${errors[id] ? 'text-rose-500' : 'text-zinc-400'}
                `}>
                    {label}
                </label>
            </div>
        )
        }

        export default Input

    -components > navbar > Button.tsx // คอยรับค่า props เพื่อกำหนดการทำงานของฟังก์ชั่นปุ่มต่างๆ

        'use client';

        import React from 'react'
        import { IconType } from 'react-icons';

        interface ButtonProps {
            label: string;
            onClick: (e:React.MouseEvent<HTMLButtonElement>) => void;
            disabled?: boolean;
            outline?: boolean;
            small?: boolean;
            icon?: IconType;
        }

        const Button:React.FC<ButtonProps> = ({
            label,
            onClick,
            disabled,
            outline,
            small,
            icon : Icon
        }) => {
        return (
            <button
                onClick={onClick}
                disabled={disabled}
                className={`
                relative
                disabled:opacity-70
                disabled:cursor-not-allowed
                rounded-lg
                hover:opacity-80
                transition
                w-full
                ${outline ? 'bg-white' : 'bg-rose-500'}
                ${outline ? 'border-black' : 'border-rose-500'}
                ${outline ? 'text-black' : 'text-white'}
                ${small ? 'py-1': 'py-3'}
                ${small ? 'text-sm' : 'text-md'}
                ${small ? 'font-light' : 'font-semibold'}
                ${small ? 'border-[1px]' : 'border-2'}
            `}>
                
                {Icon && (<Icon size={24} className='absolute left-4 top-3'/>)}
                {label}
            </button>
        )
        }

        export default Button

    -components > RegisterModal.tsx // ส่งค่าไปหา component ต่างๆเพื่อแสดงหน้า register

        'use client';
        import axios from 'axios';
        import React from 'react'
        import { AiFillGithub } from 'react-icons/ai';
        import {FcGoogle} from 'react-icons/fc'
        import { useCallback, useState } from 'react';
        import {FieldValues, SubmitHandler, useForm} from 'react-hook-form'
        import useRegisterModal from '@/app/hooks/useRegisterModal';
        import Modal from './Modal';
        import Heading from '../Heading';
        import Input from '../inputs/Input';
        import {toast} from 'react-hot-toast'
        import Button from '../navbar/Button';

        const RegisterModal = () => {
            const registerModal = useRegisterModal();
            const [isLoading, setIsLoading] = useState(false);
            
            const {register, handleSubmit, formState:{errors,}} = useForm<FieldValues>({defaultValues: {
                name: '',
                email: '',
                password: '',
            }});
            
            const onSubmit: SubmitHandler<FieldValues> = (data) => {
                setIsLoading(true);
                axios.post('/api/register', data)
                    .then(() => {
                        registerModal.onClose();
                    })
                    .catch((error) => {
                        toast.error('Some thing wrong');
                    })
                    .finally(() => {
                        setIsLoading(false);
                    })
            }

            const bodyContent = (
                <div className='flex flex-col gap-4'>
                    <Heading title='Welcome to Airbnb' subtitle='Create an account!'/>
                    <Input id='email' label='Email' disabled={isLoading} register={register} errors={errors} required />
                    <Input id='name' label='Name' disabled={isLoading} register={register} errors={errors} required />
                    <Input id='password' type='password' label='Password' disabled={isLoading} register={register} errors={errors} required />
                </div>
            );

            const footerContent = (
                <div className='flex flex-col gap-4 mt-3'>
                    <hr />
                    <Button
                        outline
                        label='Continue with Google'
                        icon={FcGoogle}
                        onClick={() => {}}
                    />
                    <Button
                        outline
                        label='Continue with Github'
                        icon={AiFillGithub}
                        onClick={() => {}}
                    />
                    <div className='text-neutral-500 text-center mt-4 font-light'>
                        <div className='justify-center flex flex-row items-center gap-2'>
                            <div>Already have an account?</div>
                            <div
                            className='text-neutral-800 cursor-pointer hover:underline'
                            onClick={registerModal.onClose}>Log in</div>
                        </div>
                    </div>
                </div>
            )

        return (
            <Modal 
                    disabled={isLoading}
                    isOpen={registerModal.isOpen}
                    title='Register'
                    actionLabel='Continue'
                    onClose={registerModal.onClose}
                    onSubmit={handleSubmit(onSubmit)}
                    body={bodyContent}
                    footer={footerContent}
            />
        )
        }

        export default RegisterModal
    
    -components > Modal.tsx // รับค่า props เพื่อกำหนดการทำงานของฟังก์ชั่นหน้าต่างแสดงข้อมูล

        'use client';

        import React, { useCallback, useEffect, useState } from 'react'
        import {IoMdClose} from 'react-icons/io';
        import Button from '../navbar/Button';

        interface ModalProps {
            isOpen?: boolean;
            onClose: () => void;
            onSubmit: () => void;
            title?: string;
            body?: React.ReactElement;
            footer?: React.ReactElement;
            actionLabel: string;
            disabled?: boolean;
            secondaryAction?: () => void;
            secondaryActionLabel?: string;
        }

        const Modal:React.FC<ModalProps> = ({
            isOpen,
            onClose,
            onSubmit,
            title,
            body,
            footer,
            actionLabel,
            disabled,
            secondaryAction,
            secondaryActionLabel,
        }) => {
        const [showModal, setShowModal] = useState(isOpen);
        
        useEffect(() => {
            setShowModal(isOpen);
        }, [isOpen]);

        const handleClose = useCallback(() => {
            if (disabled){
                return;
            }
            setShowModal(false);
            setTimeout(() => {
                onClose();
            }, 300);
        }, [disabled, onClose])

        const handleSubmit = useCallback(() => {
            if (disabled) {
                return;
            }
            onSubmit();
        }, [disabled, onSubmit]);

        const handleSecondaryAction = useCallback(() => {
            if (disabled || !secondaryAction){
            return;
            }
            secondaryAction();
        }, [disabled, secondaryAction])

        if (!isOpen){
            return null;
        }

        return (
            <>
            <div className='
                justify-center
                items-center
                flex
                overflow-x-hidden
                overflow-y-auto
                fixed
                inset-0
                z-50
                outline-none
                focus:outline-none
                bg-neutral-800/70'>
                <div className='relative w-full md:w-4/6 lg:w-3/6 xl:w-2/5 my-6 mx-auto h-fuol lg:h-auto md:h-auto'>
                {/* content */}
                <div className={`
                translate
                duration-300
                h-full
                ${showModal ? 'translate-y-0' : 'translate-y-full'}
                ${showModal ? 'opacity-100' : 'opacity-0'}
                `}>
                    <div className='
                    translate
                    h-full
                    lg:h-auto
                    md:h-auto
                    border-0
                    rounded-lg
                    shadow-lg
                    relative
                    flex
                    flex-col
                    w-full
                    bg-white
                    outline-none
                    focus:outline-none'>
                    {/* header */}
                    <div className='flex items-center p-6 rounded-t justify-center relative border-b-[1px]'>
                        <button onClick={handleClose} className='p-1 border-0 hover:opacity-70 transition absolute left-9'>
                        <IoMdClose size={18}/>
                        </button>
                        <div className='text-lg font-semibold'>
                        {title}
                        </div>
                    </div>
                    
                    {/* body */}
                    <div className='relative p-6 flex-auto'>
                        {body}
                    </div>

                    {/* footer */}
                    <div className='flex flex-col gap-2 p-6'>
                        <div className='flex flex-row items-center gap-4 w-full'>
                        {secondaryAction && secondaryActionLabel && (
                            <Button
                            outline
                            disabled={disabled}
                            label={secondaryActionLabel}
                            onClick={handleSecondaryAction}
                        />
                        )}

                        <Button 
                            disabled={disabled}
                            label={actionLabel}
                            onClick={handleSubmit}
                            />
                        </div>
                        {footer}
                    </div>

                    </div>
                </div>
                </div>
            </div>
            </>
        )
        }

        export default Modal

    -app > layout.tsx

        import './globals.css'
        import type { Metadata } from 'next'
        import { Nunito } from 'next/font/google'
        import Navbar from './components/navbar/Navbar'
        import ClientOnly from './components/ClientOnly'
        import RegisterModal from './components/modal.tsx/RegisterModal'
        import ToasterProvider from './providers/ToasterProvider'

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
                <ClientOnly>
                    <ToasterProvider/>
                    <RegisterModal/>
                    <Navbar/>
                </ClientOnly>
                {children}
            </body>
            </html>
        )
        }

    

    

    