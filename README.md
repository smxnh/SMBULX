# SMBULX
SMBULX is a powerful Go-based command-line tool for sending bulk emails via multiple SMTP servers. It supports both plain text and HTML formats, dynamic content, robust error handling, and concurrent sending for speed. SMBULX offers extensive customization and control through various command-line options.
for more https://t.me/smtpSMX

![alt text](https://raw.githubusercontent.com/smxnh/SMBULX/main/20250213_171938.jpg)

## ✓ Core Features

*   **SMTP Server Support:** Distribute your sending load across multiple SMTP servers, each with its own unique credentials. This helps prevent any single server from being overloaded and potentially flagged, increasing deliverability.
*   **Dynamic Content Replacement:** Personalize your emails on a large scale by using placeholders that are dynamically replaced with recipient-specific or campaign-specific data. This includes recipient names, sender names, and custom content like links or special offers.
*   **Attachment Support:** Easily attach files to your emails. This is useful for sending documents, promotional materials, or other relevant files.
*   **Custom Headers:** Add and manage custom email headers to fine-tune your email campaigns and meet specific requirements. This allows for detailed control over the email's metadata. Headers are shuffled to make unique emails for each send.
*   **Concurrency:** Achieve faster sending speeds by leveraging goroutines and a worker pool to send emails concurrently. The number of concurrent threads is configurable.
*   **Logging:** Get detailed logging for all email sending operations. Track connections, authentication, sending results (success/failure), and any errors encountered during the process.
*   **Retry Mechanism:** Automatically retry sending emails that fail due to temporary issues. The number of retry rounds is configurable.
*   **Recipient Domain Filtering:** Target specific groups of recipients by filtering emails based on their domain. This allows you to focus your campaigns on certain email providers or exclude unwanted addresses.
*   **Blacklisting:** Prevent emails from being sent to specific addresses by maintaining a blacklist. This helps avoid sending unwanted emails and improves your sender reputation.
*   **Flexible Message Format:** Support for both plain text and HTML email formats, enabling you to create visually appealing and informative emails.
*   **Automatic IP Detection:** Automatically retrieve the sender's public IP address for logging and tracking purposes.
*   **Valid SMTP Saving:** Automatically saves valid Smtp to file for future use.
*   **Header Shuffling**: Custom headers are shuffled from file to ensure a different set of custom headers per email

## ✓ Requirements

*   Go 1.18 or higher (installation instructions can be found at https://go.dev/doc/install

## ✓ Detailed Usage Instructions

### • Command-Line Flags: The Control Panel

SMBULX is configured using a comprehensive set of command-line flags. Each flag provides granular control over different aspects of the email sending process:

*   `-s <file>`: **(Required)** Path to the file containing SMTP server details. This file is the foundation for distributing your sending load. See the "SMTP Servers File Example" section below for the required format.

*   `-r <file>`: **(Required)** Path to the file containing recipient email addresses. This file lists all the email addresses that will receive your email. See the "Recipients File Example" section below for the required format.

*   `-b <file>`: **(Required)** Path to the file containing email subjects. This file stores a list of possible subjects for your emails, allowing for variation in subject lines. See the "Subjects File Example" section below.

*   `-m <file>`: **(Required)** Path to the file(s) containing email messages. You can specify multiple message files, separated by commas, to cycle through different message bodies. Supports `.txt` (plain text) and `.html` (HTML) files. If specifying different files , you must specify different file types (E.g message.txt,message.html).

*   `-n <file>`: **(Optional)** Path to the file containing sender names. If not provided, default server's sender name will be used. This adds a personal touch and helps prevent emails from being flagged as spam.

*   `-c <int>`: **(Optional, default: 5)** Number of messages to send per SMTP server before rotating to the next server in the list. This setting helps distribute the sending load evenly and avoids overusing any single server.

*   `-t <int>`: **(Optional, default: 1)** Number of concurrent threads (goroutines) to use for sending emails. Increasing this number can significantly improve sending speed, but be mindful of your system's resources and the limits imposed by your SMTP servers.

*   `-tm <int>`: **(Optional, default: 60)** Timeout duration in seconds for email sending operations. This includes connection establishment, authentication, and data transmission. Setting a reasonable timeout prevents the script from hanging indefinitely if a server is unresponsive.

*   `-a <file>`: **(Optional)** Path to the attachment file. This allows you to include files with your emails, such as documents, images, or promotional materials.

*   `-ch <file>`: **(Optional)** Path to the file containing custom email headers. Custom headers provide a way to add metadata to your emails and can be used for tracking, filtering, or other specialized purposes. See the "Custom Headers File Example" section below for the required format. Headers are shuffled from file to ensure unique emails each send.

*   `-l <file>`: **(Optional)** Comma-separated list of paths to files containing dynamic content (links, text, sentences, etc.). Each file corresponds to a dynamic placeholder in the email message. This allows for highly personalized email campaigns. See the "Links/Text Dynamic Placeholder File Example" section below.

*   `-re <int>`: **(Optional, default: 0)** Number of retry rounds for failed emails. If an email fails to send, the script will automatically retry sending it for the specified number of rounds. If set to 0, there is no retry attempt.

*   `-f <domain(s)>`: **(Optional)** Comma-separated list of email domains to filter recipients. Only recipients with email addresses matching these domains will be sent emails. For example, `-f yahoo.com,gmail.com` will only send emails to recipients with `@yahoo.com` or `@gmail.com` addresses.

### • File Format Examples: Building Blocks for Your Campaigns

Understanding the correct file formats is crucial for SMBULX to function properly. Here are detailed examples for each required and optional file:

#### 1.  SMTP Servers File Example:

Each line in the SMTP servers file should contain the server details in the following format, separated by pipe characters (`|`):

smtp.example.com|587|username|password
smtp.example.net|465|user2|securepass
mail.anotherdomain.org|25|sender3|password123

*   `smtp.example.com`: The SMTP server address (hostname or IP address). This is the address the script will use to connect to the server.
*   `587`: The SMTP server port number. Common ports include:
    *   `25`: Typically used for unencrypted connections (not recommended).
    *   `465`: Used for SSL/TLS encrypted connections.
    *   `587`: Used with STARTTLS to upgrade a plain connection to an encrypted one.
*   `username`: The SMTP username used for authentication with the server.
*   `password`: The SMTP password used for authentication. Ensure this is stored securely.

#### 2.  Recipients File Example:

Each line in the recipients file should contain a single recipient email address:

recipient1@example.com
user2@example.net
contact@anotherdomain.org

Ensure that each email address is valid and properly formatted.

#### 3.  Sender Names File Example:

Each line in the sender names file should contain a single sender name:

John Doe
Jane Smith
Support Team
Marketing Department

If you don't provide a sender names file, the script will extract default smtp's sender name, which can still be effective but might not be as personalized.

#### 4.  Subjects File Example:

Each line in the subjects file should contain a single email subject:

Special Offer Inside
Limited-Time Discount
Important Account Update
New Product Announcement

The script will randomly select one of these subjects for each email sent, adding variety to your campaign.

#### 5.  Message Body File Example:

The message body file(s) contain the actual content of your email. You can specify multiple files (separated by commas) to cycle through different messages. The files can be either `.txt` (plain text) or `.html` (HTML):

*   **Plain Text (`.txt`) Example:**

    ```
    Hello Recipient_Name,

    This is a plain text email. Check out our website: DYNAMIC_PLACEHOLDER0

    Thanks,
    Sender_Name
    ```

*   **HTML (`.html`) Example:**

    ```html
    <!DOCTYPE html>
    <html>
    <head>
    <title>Special Offer</title>
    </head>
    <body>
    <p>Hello Recipient_Name,</p>
    <p>This is an HTML email with a <a href="DYNAMIC_PLACEHOLDER0">special offer</a>!</p>

    <p>Sincerely,<br>
    Sender_Name</p>
    </body>
    </html>
    ```

If you provide *both* a `.txt` and an `.html` file, the script will create a *multipart email*, which includes both the plain text and HTML versions. This allows the recipient's email client to choose the best format to display. If you only provide a `.txt` file, only plain text emails will be sent.

#### 6.  Custom Headers File Example:

Each line in the custom headers file should define a custom header in the format `Header-Name: Header Value`:

X-Campaign-ID: Summer2023
X-Priority: High
List-Unsubscribe: mailto:unsubscribe@example.com

Custom headers are shuffled each send, ensuring a different array of headers per email.

**Important Considerations:**

*   Be cautious when overriding standard headers (like `From`, `To`, `Subject`). This can significantly impact deliverability and may cause your emails to be marked as spam.
*   Use custom headers responsibly for tracking, A/B testing, or providing extra information to email clients.
*   Use header shuffling carefully, as it can increase load on server.

#### 7.  Links/Text Dynamic Placeholder File Example:

These files are the key to dynamic content replacement. You can specify one or more files, each corresponding to a dynamic placeholder in your email message.

Let's say you specified three dynamic placeholder files with `-l links.txt,promocodes.txt,customtext.txt`:

*   `links.txt` will be used to replace `DYNAMIC_PLACEHOLDER0` in your messages.
*   `promocodes.txt` will be used to replace `DYNAMIC_PLACEHOLDER1` in your messages.
*   `customtext.txt` will be used to replace `DYNAMIC_PLACEHOLDER2` in your messages.

Here are examples of what those files might contain:

*   **`links.txt` (for DYNAMIC_PLACEHOLDER0):**

    ```
    https://example.com/summer-sale
    https://example.com/new-arrivals
    https://example.com/clearance
    ```

*   **`promocodes.txt` (for DYNAMIC_PLACEHOLDER1):**

    ```
    SUMMER20
    NEWCUSTOMER
    FLASH48
    ```

*   **`customtext.txt` (for DYNAMIC_PLACEHOLDER2):**

    ```
    Shop now and save!
    Don't miss out on this offer.
    Get yours before they're gone!
    ```

For each email, the script will randomly select one line from each corresponding file and use it to replace the associated placeholder.

### • Dynamic Placeholders: Unleashing Personalization

Dynamic placeholders are special tags within your email message bodies that the script automatically replaces with specific data at send time. Here's a breakdown of the available placeholders:

*   `Recipient_Name`: The recipient's name, extracted from the email address. For example, if the email address is `jane.doe@email.com`, the `Recipient_Name` will be `jane.doe`.
*   `Recipient_Email`: The recipient's full email address. This is useful for including the recipient's email address in the body of the email.
*   `Sender_Name`: The sender's name, which is either read from the sender names file or randomly generated if the file is not provided.
*   `DYNAMIC_PLACEHOLDER0`, `DYNAMIC_PLACEHOLDER1`, `DYNAMIC_PLACEHOLDER2`, and so on: These placeholders are replaced with content from the files you specify using the `-l` flag. The number corresponds to the order in which you list the files. For example, `DYNAMIC_PLACEHOLDER0` will be replaced with a line from the first file listed, `DYNAMIC_PLACEHOLDER1` with a line from the second file, and so on.


### • Blacklisting: Suppress Unwanted Emails

Prevent sending to specific email addresses. Create a `Blacklist` directory in the same folder, with `.txt` files containing email addresses to exclude (one email per line). Emails listed in the `Blacklist` are skipped. Organize addresses using multiple `.txt` files. Make sure to insert Emails on separated lines.
(e.g., `unsubscribes.txt`, `bounces.txt`). Email addresses found in *any* of these files are skipped.

### • Recipient Domain Filtering (`-f`): Target Specific Domains

Only send emails to specific email domains. Use `-f domain1.com,domain2.net,domain3` to target those domains. Recipients with other domains will be skipped. Exact match with the emails is required. Make sure you insert domains with comma separated.


### • Attachment Handling (`-a`): Add Files to Your Emails

Attach files to your emails with flexible options:

-   **Single Random Attachment:** Provide a comma-separated list of files (e.g., `-a file1.pdf,file2.jpg,file3.txt`) to randomly select and attach *one* of them to each email. This is useful for A/B testing different attachments or varying content.

-   **Multiple Attachments (Group):** Provide a hyphen-separated list of files (e.g., `-a file1.pdf-file2.jpg-file3.txt`) to attach *all* of them to each email.  This ensures a consistent set of attachments for every recipient.

-   **Single Attachment:** Specify a single file path (e.g., `-a document.pdf`) to attach the same file to all emails.


### • Putting It All Together: Script Run Example

Here's a complete example of how to run the SMBULX script with various configurations:

```bash
./SMBULX -s smtp_servers.txt -r recipients.txt -n sender_names.txt -b subjects.txt -m message.html,message.txt -c 100 -t 3 -tm 30 -a attachment.pdf -ch headers.txt -l links.txt,promocodes.txt -re 3 -f yahoo.com,gmail.com
```

### This command will:

Read SMTP server details from the smtp_servers.txt file.

Read recipient email addresses from the recipients.txt file.

Read sender names from the sender_names.txt file.

Read email subjects from the subjects.txt file.

Construct emails using the html code from the message.html and the plain text from the message.txt file, creating a multipart email.

Send 100 messages per SMTP server.

Use 3 concurrent threads for sending.

Set a timeout of 30 seconds for each email sending operation.

Attach the file attachment.pdf to each email.

Read custom headers from the headers.txt file and include them in each email. Headers will be shuffled on a per email basis

Replace DYNAMIC_PLACEHOLDER0 with content from the links.txt file.

Replace DYNAMIC_PLACEHOLDER1 with content from the promocodes.txt file.

Retry sending failed emails up to 3 times.

Only send emails to recipients with @yahoo.com or @gmail.com email addresses.

By understanding and utilizing these command-line flags and file formats, you can harness the full power of SMBULX to create targeted, personalized, and efficient email campaigns.


• Script Run real Example:

To send bulk emails with the provided configurations, use the following command:

```bash
./SMBULX -s smtp_servers.txt -r recipients.txt -n sender_names.txt -b subjects.txt -m email_messages.txt -c 100 -tm 3 -d 30 -a /path/to/attachment.pdf -ch headers.txt -l l1.txt,l2.txt,l3.txt -re 999 -f gmail
```
