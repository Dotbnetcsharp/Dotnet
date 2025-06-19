document.addEventListener('DOMContentLoaded', function () {
  feather.replace();

  // Sidebar toggle button
  const sidebar = document.querySelector('.sidebar');
  const sidebarBtns = document.querySelectorAll('.sidebar-toggle');

  sidebarBtns.forEach((btn) => {
    btn.addEventListener('click', () => {
      sidebar.classList.toggle('hidden');
      btn.classList.toggle('rotated');
    });
  });

  // Category buttons
  const showCatBtns = document.querySelectorAll('.show-cat-btn');
s
  showCatBtns.forEach((showCatBtn) => {
    const catSubMenu = showCatBtn.nextElementSibling;
    showCatBtn.addEventListener('click', (e) => {
      e.preventDefault();
      catSubMenu.classList.toggle('visible');
      const catBtnToRotate = showCatBtn.querySelector('.category__btn');
      if (catBtnToRotate) {
        catBtnToRotate.classList.toggle('rotated');
      }
    });
  });
});
