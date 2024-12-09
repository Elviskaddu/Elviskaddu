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
