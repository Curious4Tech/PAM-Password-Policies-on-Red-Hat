# PAM-Password-Policies-on-Red-Hat
A comprehensive guide for setting up and enforcing password policies on Red Hat-based Linux systems using PAM. This repository includes step-by-step instructions for installing necessary tools, configuring password quality rules, updating the PAM stack, and setting password aging policies. 


# Setting Up Password Policies on Red Hat Using PAM

This guide provides a comprehensive step-by-step process for setting up and enforcing password policies on a Red Hat-based Linux system using PAM.

---

## Prerequisites

- A Red Hat-based Linux system (e.g., RHEL, CentOS, or Rocky Linux).
- Root or sudo privileges.

---

## Step 1: Verify PAM Installation

PAM is typically pre-installed. Verify its installation with:

```bash
rpm -q pam
```

![image](https://github.com/user-attachments/assets/dc61cb8e-f6ad-4ed9-82ba-f71025bacfc7)

If not installed, install it:

```bash
sudo yum install pam
```

---

## Step 2: Install `libpwquality`

The `libpwquality` package is used to enforce password policies.

1. Install the package:
   ```bash
   sudo yum install libpwquality
   ```

![image](https://github.com/user-attachments/assets/86d302ca-6584-464e-8752-39183ecc1409)


2. Verify the installation:
   ```bash
   rpm -q libpwquality
   ```
![image](https://github.com/user-attachments/assets/743c53a3-fa95-4876-b89a-009428e04fef)

---

## Step 3: Locate Configuration Files

- **Password quality rules**: `/etc/security/pwquality.conf`
- **PAM stack configuration**: `/etc/pam.d/system-auth` and `/etc/pam.d/password-auth`

---

## Step 4: Configure Password Policies

### Edit `/etc/security/pwquality.conf`

Open the configuration file:

```bash
sudo nano /etc/security/pwquality.conf
```

Add or modify the following parameters:

```plaintext
minlen = 12              # Minimum password length
dcredit = -1             # Require at least one digit
ucredit = -1             # Require at least one uppercase letter
ocredit = -1             # Require at least one special character
lcredit = -1             # Require at least one lowercase letter
maxrepeat = 3            # Disallow more than 3 repeated characters
maxclassrepeat = 2       # Disallow more than 2 consecutive characters from the same class
```

---

## Step 5: Update the PAM Stack

### Edit `/etc/pam.d/system-auth` and `/etc/pam.d/password-auth`

1. Open the files:
   ```bash
   sudo nano /etc/pam.d/system-auth
   sudo nano /etc/pam.d/password-auth
   ```

2. Locate or add the following lines:

   ```plaintext
   password requisite pam_pwquality.so retry=3 authtok_type=
   password required pam_unix.so use_authtok remember=5
   ```

   - **`retry=3`**: Number of attempts to enter a valid password.
   - **`remember=5`**: Prevents reuse of the last 5 passwords.

---

## Step 6: Configure Password Aging

Set password expiration, minimum age, and warning period globally.

1. Edit `/etc/login.defs`:
   ```bash
   sudo nano /etc/login.defs
   ```

2. Add or modify these lines:

   ```plaintext
   PASS_MAX_DAYS 90
   PASS_MIN_DAYS 7
   PASS_WARN_AGE 14
   ```

3. Apply the settings to specific users:

   ```bash
   sudo chage -M 90 -m 7 -W 14 <username>
   ```

---

## Step 7: Test the Configuration

1. Set a password for a test user:
   ```bash
   sudo passwd <username>
   ```

2. Attempt to set weak passwords to ensure enforcement of the policies.

---

## Step 8: Audit and Troubleshoot

1. Check logs for errors:
   ```bash
   sudo tail -f /var/log/secure
   ```

2. Verify the policy configuration:
   ```bash
   sudo authconfig --test | grep -i password
   ```

---

## Backup Configuration Files

Always back up important configuration files before making changes:

```bash
sudo cp /etc/pam.d/system-auth /etc/pam.d/system-auth.bak
sudo cp /etc/security/pwquality.conf /etc/security/pwquality.conf.bak
```

---

## References

- [Red Hat Documentation](https://access.redhat.com/documentation)
- [PAM Official Documentation](https://www.linux-pam.org/)

---

## License

This project is licensed under the MIT License. Feel free to use and modify it as needed.
```
