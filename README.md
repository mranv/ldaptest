# LDAP Query Example

This project demonstrates how to use the `ldap3` crate in Rust to connect to an LDAP server, perform a simple bind, and search for user entries.

## Prerequisites

- Rust installed on your system. If not, you can install it from [rust-lang.org](https://www.rust-lang.org/).
- Access to an LDAP server.
- The `ldap3` crate, which can be added to your project by including it in your `Cargo.toml`:

  ```toml
  [dependencies]
  ldap3 = "0.9.3"
  ```

## Code Overview

This Rust code connects to an LDAP server, binds using a username and password, constructs an LDAP query, and prints out the distinguished names (DNs) of the search results.

## Usage

1. **Set Up Your LDAP Server Connection:**
   Modify the `ldap://192.168.0.110:3268` URL to match your LDAP server's address and port.

2. **Authentication:**
   Replace `CN=Administrator,CN=Users,DC=tech69,DC=local` and `Passw0rd` with your LDAP administrator's DN and password.

3. **Adjust the Search Filter:**
   The search filter is set to find all users with a `serviceprincipalname`. Adjust the filter as necessary.

## Running the Code

1. Ensure you have the `ldap3` crate installed:

   ```sh
   cargo build
   ```

2. Run the program:

   ```sh
   cargo run
   ```

## Code Explanation

Here's a step-by-step breakdown of the code:

1. **Import Dependencies:**
   ```rust
   use ldap3::*;
   use std::{process::exit, vec};
   use ldap3::result::Result;
   ```

2. **Main Function:**
   ```rust
   fn main() {
       // Establish connection to the LDAP server
       let ldap = LdapConn::new("ldap://192.168.0.110:3268");

       // Match the result of the connection attempt
       let mut ldapcon = match ldap {
           Ok(l) => l,
           Err(r) => panic!("{}", r),
       };

       // Perform a simple bind with the specified credentials
       ldapcon.simple_bind("CN=Administrator,CN=Users,DC=tech69,DC=local", "Passw0rd").unwrap();

       // Construct the search filter
       let username = "*)(serviceprincipalname=*";
       let filter = "(&(objectclass=user)(samaccountname=".to_owned() + username + "))";
       println!("filter: {}", filter);

       // Perform the search on the LDAP server
       let res = ldapcon.search("DC=tech69,DC=local", Scope::Subtree, &filter[..], vec!["dn"]).unwrap();

       // Process the search results
       let (re, ldapresult) = res.success().unwrap();
       for i in re {
           println!("{:#?}", SearchEntry::construct(i).dn);
       }
   }
   ```

## Notes

- The program will panic and exit if the LDAP connection or binding fails.
- Modify the `username` and `filter` as per your LDAP schema and search requirements.
- Ensure that the LDAP server's address, port, and credentials are correct and valid.

By following the steps above, you should be able to successfully connect to an LDAP server, perform a simple bind, and search for user entries. Adjust the parameters as necessary to fit your specific use case.
