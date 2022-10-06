<script>
  import "../app.postcss";
  import { Navbar, NavBrand, NavLi, NavUl, Dropdown, DropdownHeader, DropdownItem, DropdownDivider, Avatar, Footer, FooterCopyright, FooterLinkGroup, FooterLink } from 'flowbite-svelte';
  import { Heart } from 'svelte-heros-v2'
</script>

<Navbar let:hidden let:toggle>
    <NavBrand href="/" class="px-2">
        <span class="self-center whitespace-nowrap text-xl font-semibold dark:text-white">
          1PoundSaas
        </span>
        <Heart size="32" class="text-red-600 ml-2 mr-1" variation="solid" alt="Heart" />
        <img
                src="https://svelte.dev/_app/immutable/assets/stopwar-947a4798.svg"
                class="h-6 sm:h-9"
                alt="Svelte Logo"
        />
    </NavBrand>
    <div class="flex items-center md:order-2">
        <Avatar id="avatar-menu" border />
    </div>
    <Dropdown placement="bottom" triggeredBy="#avatar-menu">
        <DropdownHeader>
            <span class="block text-sm"> Bonnie Green </span>
            <span class="block truncate text-sm font-medium"> name@flowbite.com </span>
        </DropdownHeader>
        <DropdownItem href="/auth/reset-password">Change password</DropdownItem>
        <DropdownDivider />
        <DropdownItem>Sign out</DropdownItem>
    </Dropdown>
    <NavUl {hidden}>
        <NavLi href="/" active={true}>Home</NavLi>
        <NavLi href="/about">About</NavLi>
        <NavLi href="/services">Services</NavLi>
        <NavLi href="/pricing">Pricing</NavLi>
        <NavLi href="/contact">Contact</NavLi>
    </NavUl>
</Navbar>

<div class="px-6">
    <slot />
</div>

<Footer>
    <FooterCopyright href="https://www.1poundsaas.com/" by="1PoundSaaS™" year={2022} />
    <FooterLinkGroup ulClass="flex flex-wrap items-center mt-3 text-sm text-gray-500 dark:text-gray-400 sm:mt-0">
        <FooterLink href="/">About</FooterLink>
        <FooterLink href="/">Privacy Policy</FooterLink>
        <FooterLink href="/">Licensing</FooterLink>
        <FooterLink href="/">Contact</FooterLink>
    </FooterLinkGroup>
</Footer>
