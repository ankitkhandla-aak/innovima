innovima-website/
├── public/
│   ├── images/  # Optimized images (e.g., hero.jpg, product1.jpg)
│   └── favicon.ico
├── src/
│   ├── app/  # Next.js App Router
│   │   ├── (pages)/  # Grouped pages
│   │   │   ├── about/
│   │   │   │   └── page.js
│   │   │   ├── ai-wellness/
│   │   │   │   └── page.js
│   │   │   ├── blog/
│   │   │   │   └── page.js  # Placeholder for blog
│   │   │   ├── contact/
│   │   │   │   └── page.js
│   │   │   ├── privacy-policy/
│   │   │   │   └── page.js
│   │   │   ├── products/
│   │   │   │   ├── [slug]/
│   │   │   │   │   └── page.js  # Dynamic product detail
│   │   │   │   └── page.js  # Shop/Products page
│   │   │   ├── quiz/
│   │   │   │   └── page.js
│   │   │   ├── terms/
│   │   │   │   └── page.js
│   │   │   └── layout.js  # Shared layout for pages
│   │   ├── globals.css  # Tailwind styles
│   │   ├── layout.js  # Root layout
│   │   ├── page.js  # Home page
│   │   └── not-found.js  # 404 page
│   ├── components/  # Reusable components
│   │   ├── ui/  # Basic UI components
│   │   │   ├── Button.js
│   │   │   ├── Card.js
│   │   │   ├── Form.js
│   │   │   ├── Input.js
│   │   │   ├── Modal.js
│   │   │   └── Skeleton.js
│   │   ├── layout/  # Layout components
│   │   │   ├── Footer.js
│   │   │   ├── Header.js
│   │   │   └── Navigation.js
│   │   ├── sections/  # Page sections
│   │   │   ├── Hero.js
│   │   │   ├── ProductGrid.js
│   │   │   ├── Testimonials.js
│   │   │   └── NewsletterSignup.js
│   │   ├── ecommerce/  # Ecommerce components
│   │   │   ├── Cart.js
│   │   │   ├── CartItem.js
│   │   │   ├── CheckoutForm.js
│   │   │   └── ProductCard.js
│   │   ├── ErrorBoundary.js  # Error boundary
│   │   └── LoadingFallback.js  # Loading states
│   ├── context/  # State management
│   │   └── CartContext.js
│   ├── data/  # Static data
│   │   └── products.json  # Admin-editable product data
│   ├── hooks/  # Custom hooks
│   │   └── useFormValidation.js
│   ├── lib/  # Utilities
│   │   ├── utils.js  # Helpers (e.g., format price)
│   │   └── validationSchemas.js  # Yup schemas
│   └── styles/  # Additional styles if needed
├── package.json
├── tailwind.config.js
├── next.config.js
├── .env.local  # For secrets (e.g., form submission endpoint)
└── README.md  # Deployment instructions
{
  "name": "innovima-website",
  "version": "1.0.0",
  "scripts": {
    "dev": "next dev",
    "build": "next build",
    "start": "next start",
    "lint": "next lint"
  },
  "dependencies": {
    "next": "^14.0.0",
    "react": "^18.0.0",
    "react-dom": "^18.0.0",
    "react-hook-form": "^7.0.0",
    "yup": "^1.0.0",
    "@hookform/resolvers": "^3.0.0",
    "tailwindcss": "^3.0.0",
    "lucide-react": "^0.300.0"  // For icons (lightweight)
  },
  "devDependencies": {
    "eslint": "8.0.0",
    "eslint-config-next": "14.0.0"
  }
}import { Inter } from 'next/font/google';
import './globals.css';
import Header from '@/components/layout/Header';
import Footer from '@/components/layout/Footer';
import ErrorBoundary from '@/components/ErrorBoundary';
import { CartProvider } from '@/context/CartContext';

const inter = Inter({ subsets: ['latin'] });

export const metadata = {
  title: 'INNOVIMA - Smart Wellness Starts Here',
  description: 'AI-driven wellness brand with advanced nutraceutical products.',
  keywords: 'wellness, nutraceuticals, AI diagnosis, health supplements',
  openGraph: { title: 'INNOVIMA', description: 'Smart Wellness Starts Here' },
};

export default function RootLayout({ children }) {
  return (
    <html lang="en">
      <body className={`${inter.className} bg-teal-50 text-gray-900`}>
        <ErrorBoundary>
          <CartProvider>
            <Header />
            <main className="min-h-screen">{children}</main>
            <Footer />
          </CartProvider>
        </ErrorBoundary>
      </body>
    </html>
  );
}@tailwind base;
@tailwind components;
@tailwind utilities;

/* Custom gradients and shadows for wellness theme */
.hero-bg {
  background: linear-gradient(135deg, #14b8a6 0%, #0f766e 100%);
}
.card-shadow {
  box-shadow: 0 4px 6px -1px rgba(0, 0, 0, 0.1), 0 2px 4px -1px rgba(0, 0, 0, 0.06);
}import React from 'react';

class ErrorBoundary extends React.Component {
  constructor(props) {
    super(props);
    this.state = { hasError: false };
  }

  static getDerivedStateFromError(error) {
    return { hasError: true };
  }

  componentDidCatch(error, errorInfo) {
    console.error('Error caught by boundary:', error, errorInfo);
  }

  render() {
    if (this.state.hasError) {
      return (
        <div className="flex items-center justify-center min-h-screen bg-teal-50">
          <div className="text-center">
            <h1 className="text-2xl font-bold text-teal-700">Oops! Something went wrong.</h1>
            <p className="text-gray-600">Please refresh the page or contact support.</p>
            <button onClick={() => window.location.reload()} className="mt-4 px-4 py-2 bg-teal-600 text-white rounded">Reload</button>
          </div>
        </div>
      );
    }
    return this.props.children;
  }
}

export default ErrorBoundary;import { createContext, useContext, useState, useEffect } from 'react';

const CartContext = createContext();

export function CartProvider({ children }) {
  const [cart, setCart] = useState([]);

  useEffect(() => {
    const storedCart = localStorage.getItem('cart');
    if (storedCart) setCart(JSON.parse(storedCart));
  }, []);

  const addToCart = (product) => {
    setCart((prev) => {
      const updated = [...prev, product];
      localStorage.setItem('cart', JSON.stringify(updated));
      return updated;
    });
  };

  const removeFromCart = (id) => {
    setCart((prev) => {
      const updated = prev.filter((item) => item.id !== id);
      localStorage.setItem('cart', JSON.stringify(updated));
      return updated;
    });
  };

  return (
    <CartContext.Provider value={{ cart, addToCart, removeFromCart }}>
      {children}
    </CartContext.Provider>
  );
}

export const useCart = () => useContext(CartContext);[
  {
    "id": "hair-gummies",
    "name": "Hair Gummies",
    "image": "/images/hair-gummies.jpg",
    "benefits": ["Promotes hair growth", "Strengthens follicles"],
    "ingredients": ["Biotin", "Vitamin D"],
    "price": 29.99,
    "description": "Delicious gummies for healthy hair.",
    "faq": [{"q": "How to use?", "a": "Take 2 daily."}]
  },
  {
    "id": "skin-tablets",
    "name": "Skin Tablets",
    "image": "/images/skin-tablets.jpg",
    "benefits": ["Clears skin", "Reduces acne"],
    "ingredients": ["Zinc", "Vitamin C"],
    "price": 24.99,
    "description": "Tablets for radiant skin.",
    "faq": [{"q": "Side effects?", "a": "Minimal, consult doctor."}]
  }
  // Add more as needed
]import dynamic from 'next/dynamic';
import Hero from '@/components/sections/Hero';
import ProductGrid from '@/components/sections/ProductGrid';
import Testimonials from '@/components/sections/Testimonials';
import NewsletterSignup from '@/components/sections/NewsletterSignup';

const Home = () => {
  return (
    <div>
      <Hero />
      <section className="py-12 px-4 max-w-6xl mx-auto">
        <h2 className="text-3xl font-bold text-center mb-8">Product Highlights</h2>
        <ProductGrid />
      </section>
      <Testimonials />
      <NewsletterSignup />
    </div>
  );
};

export default Home;import Image from 'next/image';
import Button from '@/components/ui/Button';

const Hero = () => {
  return (
    <section className="hero-bg text-white py-20 px-4 text-center">
      <div className="max-w-4xl mx-auto">
        <h1 className="text-4xl md:text-6xl font-bold mb-4">Smart Wellness Starts Here</h1>
        <p className="text-xl mb-8">AI-driven wellness brand — starting with advanced nutraceutical products.</p>
        <div className="flex flex-col sm:flex-row gap-4 justify-center">
          <Button href="/products" variant="primary">Shop Now</Button>
          <Button href="/quiz" variant="secondary">Take Wellness Quiz (Coming Soon)</Button>
        </div>
      </div>
    </section>
  );
};

export default Hero;import { Suspense } from 'react';
import ProductGrid from '@/components/sections/ProductGrid';
import LoadingFallback from '@/components/LoadingFallback';

const Products = () => {
  return (
    <div className="py-12 px-4 max-w-6xl mx-auto">
      <h1 className="text-3xl font-bold mb-8">Our Products</h1>
      <Suspense fallback={<LoadingFallback />}>
        <ProductGrid />
      </Suspense>
    </div>
  );
};

export default Products;import { useState, useEffect } from 'react';
import ProductCard from '@/components/ecommerce/ProductCard';
import Skeleton from '@/components/ui/Skeleton';

const ProductGrid = () => {
  const [products, setProducts] = useState([]);
  const [loading, setLoading] = useState(true);

  useEffect(() => {
    try {
      const fetchProducts = async () => {
        const res = await fetch('/data/products.json');
        const data = await res.json();
        setProducts(data);
        setLoading(false);
      };
      fetchProducts();
    } catch (error) {
      console.error('Error loading products:', error);
      setLoading(false);
    }
  }, []);

  if (loading) return <Skeleton />;

  return (
    <div className="grid grid-cols-1 md:grid-cols-2 lg:grid-cols-3 gap-6">
      {products.map((product) => (
        <ProductCard key={product.id} product={product} />
      ))}
    </div>
  );
};

export default ProductGrid;import Image from 'next/image';
import Link from 'next/link';
import { useCart } from '@/context/CartContext';

const ProductCard = ({ product }) => {
  const { addToCart } = useCart();

  return (
    <div className="bg-white rounded-lg card-shadow p-4">
      <Image src={product.image} alt={product.name} width={300} height={200} className="rounded" />
      <h3 className="text-xl font-bold mt-4">{product.name}</h3>
      <p className="text-gray-600">{product.benefits.join(', ')}</p>
      <p className="text-teal-600 font-bold">${product.price}</p>
      <div className="mt-4 flex gap-2">
        <Link href={`/products/${product.id}`} className="px-4 py-2 bg-teal-600 text-white rounded">View Details</Link>
        <button onClick={() => addToCart(product)} className="px-4 py-2 bg-gray-200 rounded">Add to Cart</button>
      </div>
    </div>
  );
};

export default ProductCard;import { notFound } from 'next/navigation';
import Image from 'next/image';
import { useCart } from '@/context/CartContext';

const ProductDetail = async ({ params }) => {
  const { slug } = params;
  const products = await fetch(`${process.env.NEXT_PUBLIC_BASE_URL}/data/products.json`).then(res => res.json());
  const product = products.find(p => p.id === slug);

  if (!product) notFound();

  return (
    <div className="py-12 px-4 max-w-4xl mx-auto">
      <Image src={product.image} alt={product.name} width={400} height={300} />
      <h1 className="text-3xl font-bold">{product.name}</h1>
      <p>{product.description}</p>
      <p className="font-bold">${product.price}</p>
      <button onClick={() => useCart().addToCart(product)} className="px-4 py-2 bg-teal-600 text-white rounded">Add to Cart</button>
      {/* Add ingredients, FAQ, etc. similarly */}
    </div>
  );
};

export default ProductDetail;import { useForm } from 'react-hook-form';
import { yupResolver } from '@hookform/resolvers/yup';
import { contactSchema } from '@/lib/validationSchemas';
import Input from '@/components/ui/Input';
import Button from '@/components/ui/Button';

const Contact = () => {
  const { register, handleSubmit, formState: { errors } } = useForm({
    resolver: yupResolver(contactSchema),
  });

  const onSubmit = async (data) => {
    try {
      // Placeholder: Send to backend or email service
      console.log('Form submitted:', data);
      alert('Message sent!');
    } catch (error) {
      console.error('Submission error:', error);
    }
  };

  return (
    <div className="py-12 px-4 max-w-4xl mx-auto">
      <h1 className="text-3xl font-bold mb-8">Contact Us</h1>
      <form onSubmit={handleSubmit(onSubmit)} className="space-y-4">
        <Input label="Name" {...register('name')} error={errors.name?.message} />
        <Input label="Email" type="email" {...register('email')} error={errors.email?.message} />
        <textarea {...register('message')} className="w-full p-2 border rounded" placeholder="Message" />
        <Button type="submit">Send</Button>
      </form>
    </div>
  );
};

export default Contact;import * as yup from 'yup';

export const contactSchema = yup.object({
  name: yup.string().required('Name is required'),
  email: yup.string().email('Invalid email').required('Email is required'),
  message: yup.string().required('Message is required'),
});
