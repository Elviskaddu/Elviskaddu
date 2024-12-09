Public Class Form1
    ' Form Load Event - Initialize data
    Private Sub Form1_Load(sender As Object, e As EventArgs) Handles MyBase.Load
        ' Populate payment method ComboBox
        cmbPaymentMethod.Items.Add("Cash")
        cmbPaymentMethod.Items.Add("Credit Card")
        cmbPaymentMethod.Items.Add("Mobile Payment")

        ' Set default values
        dtpPurchaseDate.Value = DateTime.Now
        txtSubtotal.ReadOnly = True
        txtTax.ReadOnly = True
        txtTotalAmountDue.ReadOnly = True

        ' Store Contact Info (ReadOnly)
        txtStoreName.ReadOnly = True
        txtStorePhone.ReadOnly = True
        txtStoreEmail.ReadOnly = True
        txtStoreAddress.ReadOnly = True

        ' Set default Store Information
        txtStoreName.Text = "China Town Store Uganda"
        txtStorePhone.Text = "+256 123 456789"
        txtStoreEmail.Text = "info@chinatownstore.com"
        txtStoreAddress.Text = "123 Kampala Road, Uganda"
    End Sub

    ' Calculate Total Amount Due
    Private Sub CalculateTotal()
        Dim subtotal As Decimal = 0D
        Dim taxRate As Decimal = 0.18D ' Assuming 18% tax
        Dim discount As Decimal = 0D

        ' Calculate subtotal from DataGridView items
        For Each row As DataGridViewRow In dgvItems.Rows
            If row.Cells("Quantity").Value Is Nothing AndAlso row.Cells("Price").Value IsNot Nothing AndAlso Not IsDBNull(row.Cells("quantity").Value) AndAlso IsDBNull(row.Cells("price").Value) Then
            End If
            Dim quantity As Decimal = Convert.ToDecimal(row.Cells("Quantity").Value)
            Dim price As Decimal = Convert.ToDecimal(row.Cells("Price").Value)
            subtotal += quantity * price
        Next
' Calculate tax and discount
        Dim tax As Decimal = subtotal * taxRate
        If Decimal.TryParse(txtDiscount.Text, discount) Then
            discount = Convert.ToDecimal(txtDiscount.Text)
        End If

        ' Display calculated values
        txtSubtotal.Text = subtotal.ToString("C2")
        txtTax.Text = tax.ToString("C2")
        txtTotalAmountDue.Text = (subtotal + tax - discount).ToString("C2")
    End Sub

    ' Generate Receipt Button Click
    Private Sub btnGenerateReceipt_Click(sender As Object, e As EventArgs) Handles btnGenerateReceipt.Click
        ' Calculate totals
        CalculateTotal()

        ' Generate the receipt
        Dim receipt As New System.Text.StringBuilder()
        receipt.AppendLine(txtStoreName.Text)
        receipt.AppendLine("Receipt")
        receipt.AppendLine("-------------------------------")
        receipt.AppendLine("Date: " & dtpPurchaseDate.Value.ToString("MM/dd/yyyy"))
        receipt.AppendLine("Receipt No: " & GenerateReceiptNumber())
        receipt.AppendLine("Customer Name: " & txtCustomerName.Text)
        receipt.AppendLine("Served By: " & txtServedBy.Text)
        receipt.AppendLine("-------------------------------")
        receipt.AppendLine("Items Purchased:")

        For Each row As DataGridViewRow In dgvItems.Rows
            If row.Cells("Items").Value IsNot Nothing Then
                receipt.AppendLine(row.Cells("Items").Value.ToString() & vbTab &
                                   row.Cells("Quantity").Value.ToString() & vbTab &
                                   Convert.ToDecimal(row.Cells("Price").Value).ToString("C2"))
            End If
        Next
receipt.AppendLine("-------------------------------")
        receipt.AppendLine("Subtotal: " & txtSubtotal.Text)
        receipt.AppendLine("Tax: " & txtTax.Text)
        receipt.AppendLine("Discount: " & txtDiscount.Text)
        receipt.AppendLine("Total Amount Due: " & txtTotalAmountDue.Text)
        receipt.AppendLine("-------------------------------")
        receipt.AppendLine("Payment Method: " & cmbPaymentMethod.SelectedItem.ToString())
        If cmbPaymentMethod.SelectedItem.ToString() <> "Cash" Then
            receipt.AppendLine("Transaction ID: " & txtTransactionID.Text)
        End If
        receipt.AppendLine("-------------------------------")
        receipt.AppendLine("Thank you for shopping with us!")
        receipt.AppendLine("Store Contact: " & txtStorePhone.Text & " | " & txtStoreEmail.Text & " | " & txtStoreAddress.Text)

        ' Display the receipt in RichTextBox
        rtbReceiptDisplay.Text = receipt.ToString()
    End Sub

    ' Generate a Unique Receipt Number
    Private Function GenerateReceiptNumber() As String
        Return DateTime.Now.ToString("yyyyMMddHHmmss")
    End Function

    ' Save Receipt Button Click
    Private Sub btnSaveReceipt_Click(sender As Object, e As EventArgs) Handles btnSaveReceipt.Click
        ' Save receipt to a text file
        Dim saveFileDialog As New SaveFileDialog()
        saveFileDialog.Filter = "Text File|*.txt"
        saveFileDialog.Title = "Save Receipt"
        saveFileDialog.ShowDialog()

        If saveFileDialog.FileName <> "" Then
            System.IO.File.WriteAllText(saveFileDialog.FileName, rtbReceiptDisplay.Text)
            MessageBox.Show("Receipt saved successfully.", "Save Receipt", MessageBoxButtons.OK, MessageBoxIcon.Information)
        End If
    End Sub

    ' Print Receipt Button Click
    Private Sub btnPrintReceipt_Click(sender As Object, e As EventArgs) Handles btnPrintReceipt.Click
        ' Print the receipt
        PrintDocument1.DocumentName = "Receipt"
        PrintPreviewDialog1.Document = PrintDocument1
        PrintPreviewDialog1.ShowDialog()
    End Sub

    ' Handle the actual printing of the receipt
    Private Sub PrintDocument1_PrintPage(sender As Object, e As Printing.PrintPageEventArgs) Handles PrintDocument1.PrintPage
        e.Graphics.DrawString(rtbReceiptDisplay.Text, New Font("Courier New", 12), Brushes.Black, 100, 100)
    End Sub

   
End Class
