---
title: Reporting and Troubleshooting an issue
deprecated: false
hidden: false
metadata:
  robots: index
---
<br />

### BugSnag Error Monitoring

1. **Access Bugsnag**: Log into the Bugsnag dashboard
2. **Search Inbox**: Navigate to the Bugsnag inbox search functionality
3. **Find Issues**: Search for relevant error reports and stack traces

   <Image align="center" border={false} width="800px" src="https://files.readme.io/f5fc08cab978cdd79540d1bf374f1096f37fa1c348f716c4b54490f921e87f2f-Screenshot_2026-02-02_at_11.49.08_AM.png" />
4. **Analysis**: Use the detailed error information to understand and resolve bugs

   <Image align="center" border={false} src="https://files.readme.io/791d978aff2df65b87669997efc99828003f412ac917a3c1686e83aa18fbe981-Screenshot_2026-02-02_at_11.50.05_AM.png" />

### Reporting an issue

When a user encounters an issue in the application:

1. **Error Alert**: An alert popup is displayed on the screen.
2. **Report issue**: Click the button labelled `Upload issue info`

   <Image align="center" border={false} width="300px" src="https://files.readme.io/4241d4b1377e95253025b07dedfadaa5be2dbfe982c52c2f7bf879e6ecfc49d3-Screenshot_2026-02-02_at_11.16.37_AM.png" />

If the user's face an issue and the alert is not shown or the alert box does not contain `Upload issue info` button, they can click on `More` in the home page, and then click on `Upload app info`.

<Image align="center" border={false} width="300px" src="https://files.readme.io/37733e4cb85d86af34f6ad82ae12053fecbb53728dcf8737541f7537c8c72a9b-Screenshot_2026-02-02_at_11.47.53_AM.png" />

<br />

3. **Fixing by Avni team**: The uploaded information can be accessed from AWS S3 ('env-user-media bucket' -> 'organisation media directory name' -> 'adhoc-dump-as-zip-username-uuid') by the development team to analyze the root cause of the issue and Implement appropriate fixes.